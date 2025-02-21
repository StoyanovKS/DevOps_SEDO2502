pipeline {
    agent any  

   

    environment {
        DOTNET_VERSION = '6.0.x'
    }

    stages {
        stage('Checkout Repository') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'staging') {
                        checkout scm
                    } else {
                        echo "Not on staging branch. Skipping build."
                        currentBuild.result = 'ABORTED'
                        return
                    }
                }
            }
        }

        stage('Setup .NET') {
            steps {
                sh 'wget https://dot.net/v1/dotnet-install.sh -O dotnet-install.sh'
                sh 'chmod +x dotnet-install.sh'
                sh './dotnet-install.sh --version $DOTNET_VERSION'
            }
        }

        stage('Restore Dependencies') {
            steps {
                sh 'dotnet restore'
            }
        }

        stage('Build Project') {
            steps {
                sh 'dotnet build --no-restore'
            }
        }

        stage('Run Integration Tests') {
            steps {
                sh 'dotnet test HouseRentingSystem.Tests/HouseRentingSystem.Tests.csproj --no-build --verbosity normal'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'
            cleanWs()
        }
        success {
            echo 'Integration tests passed successfully!'
        }
        failure {
            echo 'Build or tests failed. Check logs for details.'
        }
    }
}
