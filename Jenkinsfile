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
                    docker-compose down --remove-orphans --volumes || true
                    
                    echo "=== Building and starting new containers ==="
                    docker-compose up -d --build --force-recreate
                    
                    echo "=== Verifying deployment ==="
                    docker-compose ps
                    
                    echo "=== Cleaning up unused resources ==="
                    docker image prune -af || true
                    docker volume prune -f || true
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