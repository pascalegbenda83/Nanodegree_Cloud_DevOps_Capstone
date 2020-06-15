pipeline{
  agent any
  environment {
    registryBlue = "pascalegbenda/capstone_blue_deployment"
    registryGreen = "pascalegbenda/capstone_green_deployment"
    registryCredential = 'DockerHub'
    dockertag = getDockerTag()
  }
  stages{
    stage('Lint HTML'){
      steps{
            sh 'tidy -q -e Blue-Green/Blue/index.html'
            sh 'tidy -q -e Blue-Green/Green/index.html'
      }
    }
    stage("Lint Dockerfile"){
      agent{
          docker{
              image 'hadolint/hadolint:latest-debian'
          }
        }
      steps {
          sh 'hadolint /Blue-Green/Blue/Dockerfile | tee -a hadolint_lint.txt'
          sh '''
              lintErrors=$(stat --printf="%s"  hadolint_lint.txt)
              if [ "$lintErrors" -gt "0" ]; then
                  echo "Check Error"
                  cat hadolint_lint.txt
                  exit 1
              else
                  echo "No Error"
              fi
          '''
          sh 'hadolint /Blue-Green/Green/Dockerfile | tee -a hadolint_lint.txt'
          sh '''
              lintErrors=$(stat --printf="%s"  hadolint_lint.txt)
              if [ "$lintErrors" -gt "0" ]; then
                  echo "Check Error"
                  cat hadolint_lint.txt
                  exit 1
              else
                  echo "No Error"
              fi
          '''
          }
        }
    stage('Build Docker Image'){
      steps{
        sh "docker build -t ${registryBlue}:${dockertag} -f Blue-Green/Blue/Dockerfile Blue-Green/Blue"
        sh "docker build -t ${registryGreen}:${dockertag} -f Blue-Green/Green/Dockerfile Blue-Green/Green"
      }
    }
    stage('Push Docker Image'){
      steps{
        script{
          docker.withRegistry('', registryCredential) {
            sh "docker push ${registryBlue}:${dockertag}"
            sh "docker tag ${registryBlue}:${dockertag} ${registryBlue}:latest"
            sh "docker push ${registryBlue}:latest"
            sh "docker push ${registryGreen}:${dockertag}"
            sh "docker tag ${registryGreen}:${dockertag} ${registryGreen}:latest"
            sh "docker push ${registryGreen}:latest"
          }
        }
      }
    }

    stage('Deploying to EKS'){
      steps{
        withAWS(credentials: 'K8s', region: 'us-west-2') {
          sh "aws es --region us-west-2 eks update-kubeconfig --name Blue-Green_Prod"
          sh "kubectl apply -f Blue-Green/Blue/blue-controller.json"
          sh "kubectl apply -f Blue-Green/Green/green-controller.json"
          sh "kubectl apply -f blue-green-service.json"
        }
      }
    }
  }    
}


def getDockerTag() {  
  def tag = sh script: 'git rev-parse --short=7 HEAD', returnStdout: true
  return tag.trim()
  }
