pipeline {
    agent any

    stages {
        stage('Checkout code') {
            steps {
// Checkout code from GitHub Repo and specify the branch
                git branch: 'main', url: 'https://github.com/DianaRangelova/Jenkins-SeleniumIDE-Demo'
            }
        }

        stage('Set up .NET Core') {
            steps {
// Install .Net
                bat '''
                echo Installing .NET SDK 6.0
                choco install dotnet-sdk -y --version=6.0.100
                '''
            }
        }

        stage('Uninstall Current Chrome') {
            steps {
// Install Dependencies
                bat '''
                echo Uninstalling current Google Chrome
                choco uninstall googlechrome -y
                '''
            }
        }

        stage('Install Specific Version of Chrome') {
            steps {
// Build
                bat '''
                echo Installing Google Chrome version %CHROME_VERSION%
                choco install googlechrome --version=%CHROME_VERSION% -y --allow-downgrade --ignore-checksums
                '''
            }
        }

        stage('Download and Install ChromeDriver') {
            steps {
// Run Tests
                bat '''
                echo Downloading ChromeDriver version %CHROMEDRIVER_VERSION%
                powershell -command "Invoke-WebRequest -Uri https://chromedriver.storage.googleapis.com/%CHROMEDRIVER_VERSION%/chromedriver_win32.zip -OutFile chromedriver.zip -UseBasicParsing"
                powershell -command "Expand-Archive -Path chromedriver.zip -DestinationPath ."
                powershell -command "Move-Item -Path .\\chromedriver.exe -Destination '%CHROME_INSTALL_PATH%\\chromedriver.exe' -Force"
                '''
            }
        }

        stage('Restore dependencies') {
            steps {
                // Restore dependencies using the solution file
                bat 'dotnet restore SeleniumIde.sln'
            }
        }

        stage('Build') {
            steps {
                // Build the project using the solution file
                bat 'dotnet build SeleniumIde.sln --configuration Release'
            }
        }

        stage('Run tests') {
            steps {
                // Run tests using the solution file
                bat 'dotnet test SeleniumIde.sln --logger "trx;LogFileName=TestResults.trx"'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/TestResults/*.trx', allowEmptyArchive: true
            junit '**/TestResults/*.trx'
        }
    }
}