pipeline {
    agent any

    environment {
        IMAGE_NAME = "talharehman2727/product_frontend"
        VERSION = "1.0.${BUILD_NUMBER}"
        DOCKERHUB = "dockerhub-cred"
    }

    triggers {
        githubPush()
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                sh """
                docker build -t ${IMAGE_NAME}:${VERSION} .
                docker tag ${IMAGE_NAME}:${VERSION} ${IMAGE_NAME}:latest
                """
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: DOCKERHUB,
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh """
                    echo \$PASS | docker login -u \$USER --password-stdin
                    docker push ${IMAGE_NAME}:${VERSION}
                    docker push ${IMAGE_NAME}:latest
                    """
                }
            }
        }

        stage('Deploy') {
            steps {
                sh """
                export VERSION=${VERSION}
                docker compose down || true
                docker compose pull
                docker compose up -d
                """
            }
        }
    }

    post {
        success {
            echo "✅ Frontend deployed successfully"
        }
        failure {
            echo "❌ Frontend deployment failed"
        }
    }
}
