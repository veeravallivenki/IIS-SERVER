pipeline {
    agent Built-In Node

    environment {
        DOTNET_VERSION = '8.0.302' // Specify the .NET SDK version you need
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from the repository
                checkout scm
            }
        }
        stage('Install .NET SDK') {
            steps {
                // Install the specified .NET SDK version if it's not already installed
                script {
                    def dotnetSdkInstalled = sh(script: 'dotnet --version', returnStatus: true) == 0
                    if (!dotnetSdkInstalled) {
                        sh "wget https://dot.net/v1/dotnet-install.sh -O dotnet-install.sh"
                        sh "chmod +x dotnet-install.sh"
                        sh "./dotnet-install.sh --version ${env.DOTNET_VERSION}"
                        env.PATH = "${env.WORKSPACE}/.dotnet:${env.PATH}"
                        sh "export PATH=${env.WORKSPACE}/.dotnet:${env.PATH}"
                    }
                }
                // Verify .NET SDK installation
                sh 'dotnet --version'
            }
        }
        stage('Restore') {
            steps {
                // Restore dependencies
                sh 'dotnet restore'
            }
        }
        stage('Build') {
            steps {
                // Build the application
                sh 'dotnet build --configuration Release'
            }
        }
        stage('Test') {
            steps {
                // Run unit tests
                sh 'dotnet test --configuration Release'
            }
        }
        stage('Publish') {
            steps {
                // Publish the application
                sh 'dotnet publish --configuration Release --output ./publish'
            }
        }
        stage('Archive Artifacts') {
            steps {
                // Archive the published artifacts
                archiveArtifacts artifacts: 'publish/**', allowEmptyArchive: true
            }
        }
    }
    post {
        always {
            // Clean up workspace
            cleanWs()
        }
    }
}


