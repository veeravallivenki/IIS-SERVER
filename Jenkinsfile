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
       
    }
}
