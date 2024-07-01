pipeline {
    agent any

    environment {
        DOTNET_ROOT = tool name: 'dotnet-sdk', type: 'com.cloudbees.jenkins.plugins.customtools.CustomTool'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Restore NuGet Packages') {
            steps {
                script {
                    def dotnetHome = env.DOTNET_ROOT
                    sh "${dotnetHome}/dotnet restore"
                }
            }
        }

        stage('Build Solution') {
            steps {
                script {
                    def dotnetHome = env.DOTNET_ROOT
                    sh "${dotnetHome}/dotnet build --configuration Release"
                }
            }
        }

        stage('Publish Artifacts') {
            steps {
                script {
                    def dotnetHome = env.DOTNET_ROOT
                    sh "${dotnetHome}/dotnet publish --configuration Release --output ./publish"
                }
                archiveArtifacts artifacts: 'publish/**', allowEmptyArchive: true
            }
        }
    }

    post {
        success {
            echo 'Build and publish completed successfully!'
        }
        failure {
            echo 'Build or publish failed!'
        }
    }
}

