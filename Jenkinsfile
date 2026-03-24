pipeline {
    agent any

    environment {
        IMAGE_NAME = "my-nginx"                       // Name of your Docker image
        DOCKER_REPO = "80rjan/my-nginx"               // Docker Hub namespace + repo
        REGISTRY_CREDENTIALS = "dockerhub-creds"      // Jenkins credentials ID (token)
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
                        sh """
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker tag ${IMAGE_NAME} ${DOCKER_REPO}:latest
                        docker push ${DOCKER_REPO}:latest
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            sh "docker rmi ${IMAGE_NAME} || true"
            sh "docker rmi ${DOCKER_REPO}:latest || true"
        }
    }
}
