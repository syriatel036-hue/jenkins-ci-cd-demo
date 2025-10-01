pipeline {
    agent {
        docker {
            image 'node:18'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t myapp .'
            }
        }
    }
    post {
        always {
            sh 'docker image prune -f'
        }
    }
}
