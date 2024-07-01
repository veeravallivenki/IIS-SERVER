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
         stage('Restore') {
            steps {
                script {
                    def dotnetHome = env.DOTNET_ROOT
                    sh "${dotnetHome}/dotnet restore"
                }
            }
        }
    }
}
