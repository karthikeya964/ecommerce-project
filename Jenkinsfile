pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "my-ecommerce-backend"
        REGISTRY = "karthik449/my-ecommerce"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url:'https://github.com/karthikeya964/ecommerce-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def DOCKER_TAG = env.BUILD_NUMBER
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    def DOCKER_TAG = env.BUILD_NUMBER
                    withDockerRegistry(credentialsId: 'dockerhub-credentials', url: '') {
                        sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${REGISTRY}:${DOCKER_TAG}"
                        sh "docker push ${REGISTRY}:${DOCKER_TAG}"
                    }
                }
            }
        }

        stage('Deploy to Local Docker') {
            steps {
                script {
                    def DOCKER_TAG = env.BUILD_NUMBER
                    sh '''
                    if [ $(docker ps -aq -f name=ecommerce) ]; then
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
