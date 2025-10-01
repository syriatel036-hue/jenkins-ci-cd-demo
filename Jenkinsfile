pipeline {
    agent any

    environment {
        APP_NAME = 'hello-devops'
        STAGING_PORT = '8081'
        PROD_PORT = '8080'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

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
                script {
                    docker.build("${env.APP_NAME}:${env.BUILD_ID}")
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                sh """
                    docker stop ${env.APP_NAME}-staging 2>/dev/null || true
                    docker rm ${env.APP_NAME}-staging 2>/dev/null || true
                    docker run -d \
                      --name ${env.APP_NAME}-staging \
                      -p ${env.STAGING_PORT}:8080 \
                      ${env.APP_NAME}:${env.BUILD_ID}
                """
                echo "✅ Staging deployed! Visit http://localhost:${env.STAGING_PORT}"
            }
        }

        stage('Deploy to Production') {
            input {
                message "✅ Staging looks good! Deploy to PRODUCTION?"
                ok "Deploy"
            }
            steps {
                sh """
                    docker stop ${env.APP_NAME}-prod 2>/dev/null || true
                    docker rm ${env.APP_NAME}-prod 2>/dev/null || true
                    docker run -d \
                      --name ${env.APP_NAME}-prod \
                      -p ${env.PROD_PORT}:8080 \
                      ${env.APP_NAME}:${env.BUILD_ID}
                """
                echo "🚀 PRODUCTION DEPLOYED! Visit http://localhost:${env.PROD_PORT}"
            }
        }
    }

    post {
        always {
            sh '''
                # Optional: Clean up old images (keep last 5)
                docker image prune -f
            '''
        }
    }
}
