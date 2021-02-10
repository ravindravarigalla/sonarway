pipeline {

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
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
          """
        }
      }
    }
  }
 }

        

