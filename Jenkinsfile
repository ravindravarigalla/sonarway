pipeline {

  environment {
    PROJECT = "pro1-265115"
    APP_NAME = "pro1-265115"
    FE_SVC_NAME = "${APP_NAME}-frontend"
    CLUSTER = "jenkins"
    CLUSTER_ZONE = "us-central1-c"
    IMAGE_TAG = "us.gcr.io/${PROJECT}/${APP_NAME}:latest"
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
  
  containers:
  - name: soanr
    image: sonarsource/sonar-scanner-cli
    command:
    - cat
    tty: true
  - name: gcloud
    image: gcr.io/google.com/cloudsdktool/cloud-sdk
    command:
    - cat
    tty: true
  - name: helm
    image: gcr.io/dazzling-scheme-281712/helm3-test
    command:
    - cat
    tty: true
"""
}
  }
  stages {
    stage('soanr') {
      steps {
        container('soanr') {
          sh """
            sonar-scanner \
              -Dsonar.projectKey=frontend \
              -Dsonar.sources=. \
              -Dsonar.host.url=http://34.123.57.82:9000 \
              -Dsonar.login=e71e24b67dcaf00d6592128813831449f23b2c9e
          """
        }
      }
    }
    stage('Quality gate ') {
      steps {
        container('soanr') {
          sh """
            timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
          """
        }
      }
    }
    stage('Build and push image with Container Builder') {
      steps {
        container('gcloud') {
          sh "gcloud auth list" 
          sh "PYTHONUNBUFFERED=1 gcloud builds submit -t gcr.io/dazzling-scheme-281712/node ."
        }
      }
    }
    stage('Deploy ') {
      steps {
        container('helm') {
          sh """
          #helm ls
          gcloud container clusters get-credentials cluster-1 --zone us-central1-c --project dazzling-scheme-281712
          kubectl get pods --namespace default
          kubectl create deployment nodejs --image gcr.io/dazzling-scheme-281712/node 
          kubectl expose deployment nodejs --type LoadBalancer --port 8080
          helm repo add stable https://kubernetes-charts.storage.googleapis.com/ 
          helm repo update  
          helm install sampleapp sampleapp/ --namespace default
          helm ls
          kubectl get pods --namespace default
          """ 
        }
      }
    }
  }
}
