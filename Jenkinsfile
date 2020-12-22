pipeline {

  environment {
    PROJECT = "eco-volt-297312"
    APP_NAME = "gceme"
    FE_SVC_NAME = "${APP_NAME}-frontend"
    CLUSTER = "jenkins-cd"
    CLUSTER_ZONE = "europe-west6-a"
    IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}:${env.BUILD_NUMBER}"
    JENKINS_CRED = "${PROJECT}"
  }

  agent {
    kubernetes {
      label 'sample-app'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  serviceAccountName: cd-jenkins
  containers:
  - name: gcloud
    image: gcr.io/cloud-builders/gcloud
    command:
    - cat
    tty: true
  - name: kubectl
    image: gcr.io/cloud-builders/kubectl
    command:
    - cat
    tty: true
"""
}
  }
  stages {
    stage('Clone repository') { 
      steps { 
        git branch: 'master', url: 'https://github.com/githubamid/sample-app.git'
        sh 'echo $BUILD_NUMBER'
        sh 'echo $GIT_BRANCH'
        sh 'printenv | sort'
        sh 'echo ${env.GIT_COMMIT}'
      }
    }
    stage('Test') {
      steps {
        container('gcloud') {
          sh '''
            echo "$IMAGE_TAG"
          '''
        }
      }
    }
    stage('Build and push image') {
      steps {
        container('kubectl') {
          sh 'echo "$FE_SVC_NAME"'
        }
      }
    }
  }
}
