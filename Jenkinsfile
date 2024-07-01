pipeline {
    agent {
        label 'windows'
    }
    environment {
        BUILD_CONFIGURATION = 'Release'
        SOLUTION = '**/*.sln'
        BUILD_PLATFORM = 'Any CPU'
    }

    stages {
        stage('Restore NuGet Packages') {
            steps {
                script {
                    // Ensure the right version of NuGet is installed
                    bat 'nuget restore ${env.SOLUTION}'
                }
            }
        }

        stage('Build Solution') {
            steps {
                script {
                    // Use MSBuild to build the solution
                    bat "msbuild ${env.SOLUTION} /p:Configuration=${env.BUILD_CONFIGURATION} /p:Platform=${env.BUILD_PLATFORM} /p:DeployOnBuild=true /p:WebPublishMethod=Package /p:PackageAsSingleFile=true /p:SkipInvalidConfigurations=true /p:PackageLocation=${env.WORKSPACE}\\App_Package"
                }
            }
        }

        stage('Publish Artifact') {
            steps {
                archiveArtifacts artifacts: 'App_Package/**/*.zip', allowEmptyArchive: true
            }
        }
    }
}
