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
                echo Downloading .Net 6 Sdk
                curl -l -o dotnet-sdk-6.0.132-win-x64 https://download.visualstudio.microsoft.com/download/pr/69af2ee0-5379-4cd7-9aa8-9bed36318256/cbb00555c0ad657921351a6905aa2472/dotnet-sdk-6.0.132-win-x64
                echo installing dotnet-sdk-6.0.132-win-x64
                dotnet-sdk-6.0.132-win-x64 /quiet /norestart
                '''
            }
        }

        stage('Restore Nuget Packages') {
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
            step {
                $class: 'MSTestPublisher',
                testResultsFile: '**/TestResults/*.trx'
            }
        }
    }
}