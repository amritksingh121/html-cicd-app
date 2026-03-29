pipeline {
  agent {
    docker { image 'docker:latest' }
  }
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
    IMAGE_NAME = 'amritksingh121/html-cicd-app'
  }
  stages {
    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
      }
    }
    stage('Push to DockerHub') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
        sh 'docker push $IMAGE_NAME:$BUILD_NUMBER'
      }
    }
    stage('Update Manifest Repo') {
      steps {
        sh """
          sed -i 's|$IMAGE_NAME:.*|$IMAGE_NAME:$BUILD_NUMBER|g' deployment.yaml
          git config user.email 'jenkins@cicd.com'
          git config user.name 'Jenkins'
          git add deployment.yaml
          git commit -m 'Update image to $BUILD_NUMBER'
          git push
        """
      }
    }
  }
}
