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
                    echo "Finding containers binding to port 80..."

                    PORT80=$(docker ps --format "{{.ID}}" --filter "publish=80")

                    if [ ! -z "$PORT80" ]; then
                        echo "Containers using port 80:"
                        echo "$PORT80"
                        echo "Killing them now..."
                        docker rm -f $PORT80 || true
                    else
                        echo "No running container is using port 80."
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
