pipeline {
    agent any

    environment {
        IMAGE_NAME = "my-nginx"                  // Name of your Docker image
        REGISTRY = "docker.io/your-dockerhub-username" // Docker Hub username
        REGISTRY_CREDENTIALS = "dockerhub-creds"      // Jenkins credentials ID
    }

    stages {
        stage('Clone repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/80rjan/GithubActions_test.git'
            }
        }

        stage('Build image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME} ."
                }
            }
        }

        stage('Push image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${REGISTRY_CREDENTIALS}", 
                                                     usernameVariable: 'DOCKER_USER', 
                                                     passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin $REGISTRY"
                        sh "docker tag ${IMAGE_NAME} ${REGISTRY}/${IMAGE_NAME}:latest"
                        sh "docker push ${REGISTRY}/${IMAGE_NAME}:latest"
                    }
                }
            }
        }
    }

    post {
        always {
            // Optional: clean up local images
            sh "docker rmi ${IMAGE_NAME} || true"
            sh "docker rmi ${REGISTRY}/${IMAGE_NAME}:latest || true"
        }
    }
}
