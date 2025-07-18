pipeline {
    agent any

    environment {
        GKE_CLUSTER_NAME = 'energytech-global-cluster'
        GKE_PROJECT_ID = 'todaybatch-16june'
        GKE_ZONE = 'us-central1-a'
        GIT_REPO_URL = 'https://github.com/avinash943/k8s-cicd.git'
        GIT_BRANCH = 'main'
    }

    stages {
        stage('Checkout Git Repository') {
            steps {
                git url: "${GIT_REPO_URL}", branch: "${GIT_BRANCH}"
            }
        }

        stage('Authenticate with GCP') {
            steps {
                withCredentials([file(credentialsId: 'gcp-service-account', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    sh '''
                        echo "Authenticating with GCP"
                        gcloud auth activate-service-account --key-file="$GOOGLE_APPLICATION_CREDENTIALS"
                        gcloud config set project $GKE_PROJECT_ID
                        gcloud config set compute/zone $GKE_ZONE
                    '''
                }
            }
        }

        stage('Configure kubectl') {
            steps {
                sh '''
                    echo "Getting GKE cluster credentials"
                    gcloud container clusters get-credentials $GKE_CLUSTER_NAME
                '''
            }
        }

        stage('Deploy to GKE') {
            steps {
                sh '''
                    echo "Applying Kubernetes manifests"
                    kubectl apply -f aks-store-quickstart.yaml
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    echo "Checking deployment status"
                    kubectl get pods
                    kubectl get svc
                '''
            }
        }
    }
}