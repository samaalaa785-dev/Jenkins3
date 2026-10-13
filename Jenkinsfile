pipeline {
    agent any

    environment {
        APP_NAME = 'python-app'
        REPO_URL = 'https://github.com/samaalaa785-dev/Jenkins3.git'
    }

    stages {
        stage('Getting Repo files') {
            steps {
                git branch: 'main',
                    credentialsId: 'jenkins',
                    url: "${REPO_URL}"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${APP_NAME}:${BUILD_NUMBER} .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                            -u "$DOCKER_USERNAME" --password-stdin

                        docker tag ${APP_NAME}:${BUILD_NUMBER} \
                            "$DOCKER_USERNAME/${APP_NAME}:${BUILD_NUMBER}"

                        docker push "$DOCKER_USERNAME/${APP_NAME}:${BUILD_NUMBER}"

                        docker logout
                    '''
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                    docker run -d \
                        --name "${APP_NAME}-main-${BUILD_NUMBER}" \
                        -p 5000:5000 \
                        "${APP_NAME}:${BUILD_NUMBER}"

                    docker ps
                '''
            }
        }
    }
}
