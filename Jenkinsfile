pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/<your-username>/<your-repo>.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                      sonar-scanner \
                      -Dsonar.projectKey=cicd-poc \
                      -Dsonar.sources=.
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t cicd-poc:latest .'
            }
        }

        stage('Deploy Application') {
            steps {
                sh '''
                  docker rm -f cicd-app || true
                  docker run -d -p 8081:80 --name cicd-app cicd-poc:latest
                '''
            }
        }
    }
}
