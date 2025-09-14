pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds'   // Jenkins credentials ID
        IMAGE_NAME = 'vineethakondepudi/resturent'
        IMAGE_TAG = '${BUILD_NUMBER}'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS}", usernameVariable: "DOCKER_USER", passwordVariable: "DOCKER_PASS")]) {
                        sh """
                          echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                          docker push ${IMAGE_NAME}:${IMAGE_TAG}
                          docker logout
                        """
                    }
                }
            }
        }

       stage('Run Container') {
    steps {
        script {
            sh """
              docker stop resturent || true
              docker rm resturent || true
              docker run -d -p 8091:80 --name resturent ${IMAGE_NAME}:${IMAGE_TAG}
            """
        }
    }
}

    }
}

