pipeline {
    agent any

    environment {
        DOTNET_VERSION = '6.0.x'
    }

    stages {
        stage('Checkout Repository') {
            steps {
                checkout scm
            }
        }

        stage('Setup .NET') {
            steps {
                bat """
                echo Installing .NET ${DOTNET_VERSION}
                choco install dotnet-sdk --version=${DOTNET_VERSION} -y
                """
            }
        }

        stage('Restore Dependencies') {
            steps {
                bat 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                bat 'dotnet build --no-restore'
            }
        }

        stage('Run Tests') {
            steps {
                bat 'dotnet test --no-build --verbosity detailed --logger trx --results-directory TestResults/'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'TestResults/**/*.trx', fingerprint: true
        }
    }
}
