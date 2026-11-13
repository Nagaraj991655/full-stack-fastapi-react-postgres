pipeline {
    agent any

    environment {
        COMPOSE_PROJECT_NAME = 'fullstack-app'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', 
                    credentialsId: 'github-ssh-key', 
                    url: 'https://github.com/Nagaraj991655/full-stack-fastapi-react-postgres.git'
            }
        }
        
        stage('Automatic Cleanup & Port Release') {
            steps {
                // Bring down current compose project if running
                sh 'docker compose down --remove-orphans || true'
                
                // Automatically find and force-remove any leftover container holding port 5432
                sh '''
                    CONFLICTING_CONTAINER=$(docker ps -q --filter publish=5432)
                    if [ ! -z "$CONFLICTING_CONTAINER" ]; then
                        echo "Port 5432 is occupied by container(s): $CONFLICTING_CONTAINER. Forcibly removing..."
                        docker rm -f $CONFLICTING_CONTAINER
                    fi
                '''
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker compose build --no-cache'
            }
        }

        stage('Deploy Stack') {
            steps {
                sh 'docker compose up -d'
            }
        }
        
        stage('Health Check') {
            steps {
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
