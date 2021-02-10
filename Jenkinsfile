pipeline {

  environment {
    PROJECT = "halodoc-fisclouds"
    APP_NAME = "lazarus"
    FE_SVC_NAME = "${APP_NAME}"
    CLUSTER = "gke-cicd"
    CLUSTER_ZONE = "us-central1-c"
    IMAGE_TAG = "us.gcr.io/${PROJECT}/${APP_NAME}:latest"
    JENKINS_CRED = "${PROJECT}"
  }

  agent {
    kubernetes {
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  
  containers:
  - name: sonar
    image:   sonarsource/sonar-scanner-cli
    command:
    - cat
    tty: true
 
"""   
}
  }
   stages {
    stage('sonar') {
      steps {
        container('sonar') {
          sh ""
            sonar-scanner \
              -Dsonar.projectKey=frontend1 \
              -Dsonar.sources=. \
              -Dsonar.host.url=http://34.123.57.82:9000 \
              -Dsonar.login=0b7ea1c67da39094935a345e7915e2faeed229e6
          """
        }
      }
    }
   }
 }
