pipeline {
    agent any
    stages {
        stage('Linting HTML & Dockerfile'){
            steps {
                sh 'tidy -q -e Blue-Green/Blue/*.html'
                sh 'tidy -q -e Blue-Green/Green/*.html'

            }
        }
        stage('Build and Publish Docker Image'){
                    steps {
                        sh 'docker build -t pascalegbenda/blue_image -f Blue-Green/Blue/Dockerfile Blue-Green/Blue'
                        sh 'docker build -t pascalegbenda/green_image -f Blue-Green/Green/Dockerfile Blue-Green/Green'
                        sh 'docker push pascalegbenda/blue_image'
                        sh 'docker push pascalegbenda/green_image'
                        sh 'docker rmi -f pascalegbenda/green_image'
                        sh 'docker rmi -f pascalegbenda/blue_image'
                    }
                }
    }
}
