
pipeline {
    agent any

    environment {
        DOTNET_VERSION = '8.0.302' // specify your .NET version
        IIS_SERVER = 'https://103.99.10.72' // replace with your IIS server address
        IIS_USER = 'DESKTOP-8I53PQC' // replace with your IIS user
        IIS_PASSWORD = 'Venki@293' // replace with your IIS password (consider using credentials store)
        REMOTE_PATH = 'D:\\coreapp' // replace with your IIS deployment path
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
                // Install the specified .NET SDK version
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
               
            }
        }
        stage('Build') {
            steps {
                // Build the application
                sh 'dotnet build --configuration Release'
            }
        }
        stage('Publish') {
            steps {
                // Publish the application
                sh 'dotnet publish --configuration Release --output ./publish'
            }
        }
        stage('Transfer to IIS') {
            steps {
                script {
                    // Zip the publish folder
                    sh 'zip -r publish.zip ./publish'
                    // Transfer the zip file to the IIS server
                    sh "scp publish.zip ${IIS_USER}@${IIS_SERVER}:${REMOTE_PATH}"
                    // Connect to the IIS server and unzip the file
                    sh "ssh ${IIS_USER}@${IIS_SERVER} 'powershell -Command \"Expand-Archive -Path ${REMOTE_PATH}\\publish.zip -DestinationPath ${REMOTE_PATH} -Force\"'"
                    // Remove the zip file from the IIS server
                    sh "ssh ${IIS_USER}@${IIS_SERVER} 'del ${REMOTE_PATH}\\publish.zip'"
                }
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
