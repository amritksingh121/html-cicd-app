pipeline {
  agent any
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
    IMAGE_NAME = 'amritksingh121/html-cicd-app'
  }
  stages {
    stage('Build and Push Docker Image') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
        sh 'docker buildx create --use --name multibuilder || true'
        sh 'docker buildx build --platform linux/amd64,linux/arm64 -t $IMAGE_NAME:$BUILD_NUMBER --push .'
      }
    }
    stage('Update Manifest Repo') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'github-creds', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
          sh """
            rm -rf html-cicd-app-manifests
            git clone https://$GIT_USER:$GIT_TOKEN@github.com/amritksingh121/html-cicd-app-manifests.git
            cd html-cicd-app-manifests
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
}
