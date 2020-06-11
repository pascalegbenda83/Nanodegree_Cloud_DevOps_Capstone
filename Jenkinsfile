pipeline {
    agent any
    stages {
        stage('Linting HTML & Dockerfile'){
            steps {
                sh 'tidy -q -e Blue-Green/Blue/*.html'
                sh 'tidy -q -e Blue-Green/Green/*.html'
                sh 'hadolint Blue-Green/Blue/Dockerfile'
                sh 'hadolint Blue-Green/Green/Dockerfile'
            }
        }
        stage('Build and Publish Docker Image'){
                    steps {
                        sh 'docker build -t pascalegbenda/blueimage -f Blue-Green/Blue/Dockerfile Blue-Green/Blue'
                        sh 'docker build -t pascalegbenda/greenimage -f Blue-Green/Green/Dockerfile Blue-Green/Green'
                        sh 'docker push pascalegbenda/blue_image'
                        sh 'docker push pascalegbenda/green_image'
                        sh 'docker rmi -f pascalegbenda/green_image'
                        sh 'docker rmi -f pascalegbenda/blue_image'
                    }
                }
    }
}
