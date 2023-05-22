pipeline {
  agent any
  
  stages {
    stage('Checkout') {
      steps {
            git ''
            }
    }
    stage('Build Docker Image') {
      steps {
        sh 'docker build -t my-app .'
      }
    }
    stage('Push Docker Image to JFrog') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'jfrog-credentials', usernameVariable: 'JFROG_USER', passwordVariable: 'JFROG_PASS')]) {
          sh 'jfrog rt docker-push my-app:latest my-docker-repo --url=https://my-jfrog-url.com/artifactory --user=$JFROG_USER --password=$JFROG_PASS'
        }
      }
    }
    stage('Create Helm Chart') {
      steps {
        sh 'helm create my-app'
        // edit the my-app/values.yaml file as needed
      }
    }
    stage('Push Helm Chart to JFrog') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'jfrog-credentials', usernameVariable: 'JFROG_USER', passwordVariable: 'JFROG_PASS')]) {
          sh 'jfrog rt u my-app/ my-helm-charts --url=https://my-jfrog-url.com/artifactory --user=$JFROG_USER --password=$JFROG_PASS'
        }
      }
    }
    stage('Deploy to GKE') {
      steps {
        withCredentials([gcpServiceAccount(credentialsId: 'gcp-credentials', accountEmailVariable: 'GCP_SA_EMAIL', jsonKeyVariable: 'GCP_SA_KEY')]) {
          sh 'gcloud container clusters get-credentials my-gke-cluster --zone us-central1-a --project my-gcp-project --account=$GCP_SA_EMAIL --key-file=$GCP_SA_KEY'
          sh 'helm upgrade --install my-app my-helm-charts/my-app --set image.tag=latest'
        }
      }
    }
  }
}
