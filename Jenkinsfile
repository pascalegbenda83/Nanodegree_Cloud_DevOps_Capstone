pipeline{
  agent any
  environment {
    registryBlue = "pascalegbenda/blue-image"
    registryGreen = "pascalegbenda/green-image"
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
          sh 'hadolint Blue-Green/Blue/Dockerfile | tee -a hadolint_lint.txt'
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
          sh 'hadolint Blue-Green/Green/Dockerfile | tee -a hadolint_lint.txt'
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
        sh "docker build -f Blue/Dockerfile Blue -t ${registryBlue}:${dockertag}"
        sh "docker build -f Green/Dockerfile Green -t ${registryGreen}:${dockertag}"
      }
    }
    stage('Push Docker Image'){
      steps{
        script{
          docker.withRegistry('', registryCredential) {
            sh "docker push ${registryBrue}:${dockertag}"
            sh "docker tag ${registryBrue}:${dockertag} ${registryBrue}:latest"
            sh "docker push ${registryBrue}:latest"
            sh "docker push ${registryGreen}:${dockertag}"
            sh "docker tag ${registryGreen}:${dockertag} ${registryGreen}:latest"
            sh "docker push ${registryGreen}:latest"
          }
        }
      }
    }
