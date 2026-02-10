pipeline {
    agent any
    tools {
        maven 'Maven3'
    }

    environment {
        IMAGE_NAME = "local-jenkins-app"
        CONTAINER_NAME = "jenkins_app_container"
        SONAR_PROJECT_KEY = "project8"
        SONAR_HOST_URL = "http://localhost:9000"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/navya-tatikonda/Project8.git'
            }
        }

        stage('SonarQube Code Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                    sh '''
                    /opt/homebrew/bin/sonar-scanner \
                        -Dsonar.projectKey=project8 \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.login=$SONAR_TOKEN
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $IMAGE_NAME .
                '''
            }
        }

        stage('Stop Old Container') {
            steps {
                sh '''
                docker stop $CONTAINER_NAME || true
                docker rm $CONTAINER_NAME || true
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                docker run -d \
                  --name $CONTAINER_NAME \
                  -p 8081:8080 \
                  $IMAGE_NAME
                '''
            }
        }
    }

    post {
        success {
            echo "SonarQube analysis + deployment successful "
        }
        failure {
            echo "Pipeline failed "
        }
    }
}
