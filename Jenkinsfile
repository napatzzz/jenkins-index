pipeline {
    agent any

    stages {

        stage('Build only PR to production') {
            when {
                allOf {
                    environment name: 'CHANGE_ID', value: '.+'
                    environment name: 'CHANGE_TARGET', value: 'production'
                    environment name: 'CHANGE_BRANCH', value: 'dev'
                }
            }
            steps {
                echo "Running PR pipeline from dev → production"
            }
        }

        stage('Pull Code') {
            steps {
                git branch: 'dev', url: 'https://github.com/napatzzz/jenkins-index.git'
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                sh '''
                    echo "=== Stopping existing containers ==="
                    docker rm -f $(docker ps -aq) || true
                    
                    docker rmi -f $(docker images -q) || true
                

                '''
            }
        }
    }
    
    post {
        success {
            echo "✅ Deployment successful!"
        }
        failure {
            echo "❌ Deployment failed!"
            sh 'docker-compose logs --tail=50'
        }
    }
}