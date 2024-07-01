pipeline {
    agent any

    environment {
        DOTNET_ROOT = tool name: 'dotnet-sdk', type: 'com.cloudbees.jenkins.plugins.customtools.CustomTool'
        IIS_SERVER = 'http://103.99.10.72'
        SITE_NAME = 'dotnetapp'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Restore and Build') {
            steps {
                script {
                    def dotnetHome = env.DOTNET_ROOT
                    sh "${dotnetHome}/dotnet restore"
                    sh "${dotnetHome}/dotnet publish --configuration Release --output ./publish"
                }
            }
        }

        stage('Deploy to IIS') {
            steps {
                script {
                    def iisServer = env.IIS_SERVER
                    def siteName = env.SITE_NAME
                    def publishDir = './publish'
                    
                    // Clear existing site content
                    bat "appcmd stop site /site.name:\"${siteName}\""
                    bat "appcmd delete app /app.name:\"${siteName}\""
                    
                    // Publish to IIS
                    bat "xcopy /s \"${publishDir}\" \"${iisServer}\\${siteName}\""
                    
                    // Start the site
                    bat "appcmd start site /site.name:\"${siteName}\""
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
