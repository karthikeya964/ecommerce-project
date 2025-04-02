pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "my-ecommerce-backend"
        REGISTRY = "karthik449/my-ecommerce"
        DOCKER_TAG = "${BUILD_NUMBER}" // Defined globally
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', credentialsId: 'Github', url: 'https://github.com/karthikeya964/ecommerce-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docke-hub-cred', url: '') { // Fixed credentials ID
                        sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${REGISTRY}:${DOCKER_TAG}"
                        sh "docker push ${REGISTRY}:${DOCKER_TAG}"
                    }
                }
            }
        }

        stage('Deploy to Local Docker') {
            steps {
                script {
                    sh '''
                    if [ "$(docker ps -aq -f name=ecommerce)" ]; then
                        docker stop ecommerce
                        docker rm ecommerce
                    fi
                    docker run -d -p 5000:5000 --name ecommerce ${REGISTRY}:${DOCKER_TAG}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful!"
        }
        failure {
            echo "❌ Deployment Failed!"
        }
    }
}
