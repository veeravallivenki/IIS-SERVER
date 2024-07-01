pipeline {
    agent any
    
    environment {
        nugetPath = tool name: 'NuGet', type: 'nuget'
        dotnetPath = tool name: 'dotnet', type: 'sdk'
        msbuildPath = 'C:\\Program Files (x86)\\Microsoft Visual Studio\\2019\\BuildTools\\MSBuild\\Current\\Bin\\MSBuild.exe'
        iisWebSiteName = '103.99.10.72' // Replace with your IIS website name
        iisAppPoolName = 'dotnetapp' // Replace with your IIS app pool name
        publishDir = 'D:\\dotnetapp' // Replace with your IIS publish directory
    }
    
    stages {
        stage('Restore NuGet Packages') {
            steps {
                script {
                    def nuget = bat(returnStdout: true, script: "where nuget.exe").trim()
                    if (nuget == "") {
                        echo 'NuGet not found, installing...'
                        def nugetPath = tool name: 'NuGet', type: 'nuget'
                        bat "${nugetPath} restore YourSolution.sln"
                    } else {
                        bat "nuget restore YourSolution.sln"
                    }
                }
            }
        }
        
        stage('Build Solution') {
            steps {
                script {
                    bat "${dotnetPath} build YourSolution.sln"
                }
            }
        }
        
        stage('Publish Artifacts') {
            steps {
                script {
                    bat "${dotnetPath} publish YourProject.csproj --configuration Release --output ${env.publishDir}"
                }
            }
        }
        
        stage('Deploy to IIS') {
            steps {
                script {
                    bat "msdeploy -verb:sync -source:contentPath=${env.publishDir} -dest:contentPath=C:\\inetpub\\wwwroot\\${env.iisWebSiteName},computerName=localhost"
                }
            }
        }
    }
    
    post {
        always {
            // Clean up steps if needed
        }
    }
}
