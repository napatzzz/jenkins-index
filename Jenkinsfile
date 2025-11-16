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
                git branch: 'main', url: 'https://github.com/napatzzz/jenkins-index.git'
            }
        }

        stage('Cleanup Docker') {
            steps {
                sh '''
                    echo "Stopping and removing containers..."
                    docker-compose down --remove-orphans || true

                    echo "Removing all dangling images..."
                    docker image prune -af || true

                    echo "Removing unused volumes..."
                    docker volume prune -f || true

                    echo "Removing unused networks..."
                    docker network prune -f || true
                '''
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                sh 'docker-compose up -d --build'
            }
        }
    }
}
