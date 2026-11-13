pipeline {
    agent any

    environment {
        // Define your Compose project name so Docker manages networks/volumes correctly
        COMPOSE_PROJECT_NAME = 'fullstack-app'
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Pulls code from your GitHub repo using the SSH credentials configured in Jenkins
                git branch: 'main', 
                    credentialsId: 'ssh-github', 
                    url: 'https://github.com/Nagaraj991655/full-stack-fastapi-react-postgres.git'
            }
        }
        
        stage('Stop Existing Containers') {
            steps {
                sh 'docker compose down --remove-orphans'
            }
        }

        stage('Build Docker Images') {
            steps {
                // Build images cleanly with no-cache to pick up latest code changes
                sh 'docker compose build --no-cache'
            }
        }

        stage('Deploy Stack') {
            steps {
                // Spin up containers in detached mode
                sh 'docker compose up -d'
            }
        }
        
        stage('Health Check') {
            steps {
                // Quick sleep to let services initialize, then verify container statuses
                sleep time: 5, unit: 'SECONDS'
                sh 'docker ps'
            }
        }
    }
    
    post {
        success {
            echo '🎉 Pipeline completed successfully! Your full-stack application is live.'
        }
        failure {
            echo '❌ Pipeline failed. Please review the console output for error details.'
        }
    }
}
