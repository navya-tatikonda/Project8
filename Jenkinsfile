pipeline {
    agent any

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
                withSonarQubeEnv('SonarQube') {
                    sh '''
                    sonar-scanner \
                      -Dsonar.projectKey=$SONAR_PROJECT_KEY \
                      -Dsonar.sources=. \
                      -Dsonar.language=java \
                      -Dsonar.java.binaries=target
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
