pipeline {
agent{
    kubernetes{
        inheritFrom 'slave'
    }
}
   
environment {
        imagename = "myjenkins-docker-deploy"
        PROJECT_ID = 'eminent-ember-374712'
        CLUSTER_NAME = 'pushpak-cluster'
        LOCATION = 'us-central1-b'
        CREDENTIALS_ID = 'eminent-ember-374712'
    }

    stages{
        stage("Checkout code") {
            steps {
            git 'https://github.com/pushpak838/nodejs-demo.git'
            }
        }
        
        stage("Building Application Docker Image"){
            steps{
                script{
                    sh 'gcloud auth configure-docker asia-south1-docker.pkg.dev'
                    sh 'docker build . -t us-central1-docker.pkg.dev/eminent-ember-374712/dajrepo:$imagename'
                    }
                }
            }
        stage("Pushing Application Docker Image to Google Artifact Registry"){
            steps{
                script{
                    sh 'docker push us-central1-docker.pkg.dev/eminent-ember-374712/dajrepo:$imagename'
        }}}
        stage ("Updating Deployment Manifest") {
            steps {
                script {
                    sh 'sed -i s+us-central1-docker.pkg.dev/eminent-ember-374712/dajrepo:v1+us-central1-docker.pkg.dev/eminent-ember-374712/dajrepo:$imagename+g gke-dynamic-agent/manifests/deployment.yaml'
                }
            }
        }
        stage("Application Deployment on Google Kubernetes Engine"){
            steps{
                script{
                    sh "gcloud container clusters get-credentials app-cluster --zone $LOCATION --project $CREDENTIALS_ID"
                    sh 'kubectl apply -f gke-dynamic-agent/manifests/.'
                }
            }
        }
    }
    }
