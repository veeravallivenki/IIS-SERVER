pipeline {
    agent any

    environment {
        // Define any environment variables here
        PATH = "$PATH:$WORKSPACE/nuget"
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
                        if ! command -v mono &> /dev/null; then
                            echo "Mono could not be found. Installing Mono..."
                            sudo apt-get update
                            sudo apt-get install -y mono-complete
                        fi
                        if ! command -v nuget &> /dev/null; then
                            echo "NuGet could not be found. Installing NuGet..."
                            mkdir -p nuget
                            curl -o nuget/nuget.exe https://dist.nuget.org/win-x86-commandline/latest/nuget.exe
                            chmod +x nuget/nuget.exe
                        fi
                        mono nuget/nuget.exe restore
                        '''
                    } else {
                        bat '''
                        if not exist "nuget\\nuget.exe" (
                            echo "NuGet could not be found. Installing NuGet..."
                            mkdir nuget
                            powershell -command "Invoke-WebRequest -Uri https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -OutFile nuget\\nuget.exe"
                        )
                        nuget\\nuget.exe restore
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
                    def remote = [:]
                    remote.name = 'IIS Server'
                    remote.user = 'DESKTOP-8I53PQC'
                    remote.password = 'Venki@293'
                    remote.allowAnyHosts = true

                    // Transfer the artifacts to the IIS server
                    sshPut remote: remote, from: 'publish/', into: '/path/on/iis/server/'

                    // Execute commands on the IIS server to deploy the application
                    sshCommand remote: remote, command: '''
                        # Stop the application pool
                        appcmd stop apppool /apppool.name:dotnetapp

                        # Copy the published files to the IIS directory
                        xcopy /s /y /i /path/on/iis/server/* "D:\\dotnetapp"

                        # Start the application pool
                        appcmd start apppool /apppool.name:dotnetapp
                    '''
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
