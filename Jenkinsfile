pipeline {
    agent {
        label 'windows'
    }
    environment {
        DOTNET_VERSION = '8.0.302' // Specify the .NET SDK version you need
    }

    stages {
        stage('Build') {
            steps {
                script {
                    
                    bat "dotnet restore"
                    bat "dotnet build --configuration Release"
                }
            }
        }
        stage('Publish') {
            steps {
                script {
                    
                    bat "dotnet publish --no-restore --configuration Release --output .\\publish"
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'coreuser', passwordVariable: 'CREDENTIAL_PASSWORD', usernameVariable: 'CREDENTIAL_USERNAME')]) {
                    powershell '''
                    
                    $credentials = New-Object System.Management.Automation.PSCredential($env:CREDENTIAL_USERNAME, (ConvertTo-SecureString $env:CREDENTIAL_PASSWORD -AsPlainText -Force))

                    
                    New-PSDrive -Name X -PSProvider FileSystem -Root "\\\\DESKTOP-8I53PQC\\coreapp" -Persist -Credential $credentials

                    
                    Copy-Item -Path '.\\publish\\*' -Destination 'X:\' -Force

                    
                    Remove-PSDrive -Name X
                    '''
                 }
             }
          }
      }
   }
}

