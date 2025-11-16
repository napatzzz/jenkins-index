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

        stage('Deploy with Docker Compose') {
            steps {
                sh 'docker-compose down || true'
                sh 'docker-compose up -d --build'
            }
        }
    }
}

