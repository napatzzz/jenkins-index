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

        stage('Kill Existing Port 80 Containers') {
            steps {
                sh '''
                    echo "Checking for containers using port 80..."
                    PORT80=$(docker ps --format "{{.ID}} {{.Ports}}" | grep ":80->" | awk '{print $1}')

                    if [ ! -z "$PORT80" ]; then
                        echo "Found container(s) using port 80:"
                        echo "$PORT80"
                        echo "Stopping and removing them..."
                        docker rm -f $PORT80 || true
                    else
                        echo "No container is using port 80."
                    fi
                '''
            }
        }

        stage('Cleanup Docker') {
            steps {
                sh '''
                    echo "Running docker-compose cleanup..."
                    docker-compose down --remove-orphans || true
                    docker image prune -af || true
                    docker volume prune -f || true
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
