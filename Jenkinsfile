pipeline {
    agent any

    environment {
        // Define any environment variables here
        PATH = "$PATH:/path/to/nuget"
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
                    if (isUnix()) {
                        sh '''
                        if ! command -v nuget &> /dev/null; then
                            echo "NuGet could not be found. Installing NuGet..."
                            sudo apt-get update
                            sudo apt-get install -y nuget
                        fi
                        nuget restore
                        '''
                    } else {
                        bat '''
                        if not exist "C:\\path\\to\\nuget.exe" (
                            echo NuGet could not be found. Installing NuGet...
                            powershell -command "Invoke-WebRequest -Uri https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -OutFile C:\\path\\to\\nuget.exe"
                        )
                        C:\\path\\to\\nuget.exe restore
                        '''
                    }
                }
            }
        }

        stage('Build Solution') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'msbuild /p:Configuration=Release'
                    } else {
                        bat 'msbuild /p:Configuration=Release'
                    }
                }
            }
        }

        stage('Publish Artifacts') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'msbuild /t:Publish /p:Configuration=Release /p:OutputPath=./publish'
                    } else {
                        bat 'msbuild /t:Publish /p:Configuration=Release /p:OutputPath=./publish'
                    }
                }
                archiveArtifacts artifacts: 'publish/**', allowEmptyArchive: true
            }
        }

        stage('Deploy to IIS') {
            steps {
                script {
                    // Windows-specific steps to deploy to IIS
                    if (isUnix()) {
                        error('Deployment to IIS is only supported on Windows nodes.')
                    } else {
                        bat """
                        # Stop the application pool
                        appcmd stop apppool /apppool.name:dotnetapp

                        # Copy the published files to the IIS directory
                        xcopy /s /y /i publish\\* "D:\\dotnetapp"

                        # Start the application pool
                        appcmd start apppool /apppool.name:dotnetapp
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}

