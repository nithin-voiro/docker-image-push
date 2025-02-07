pipeline {
    agent any

    environment {
        GCP_PROJECT_ID = 'YOUR_PROJECT_ID'
        GCP_REGION = 'us-central1'
        REPO_NAME = 'my-docker-repo'
        IMAGE_NAME = 'sample-app'
        SERVICE_ACCOUNT_CREDENTIALS = credentials('gcp-service-account')
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/your-repo/sample-app.git'
            }
        }

        stage('Authenticate with Google Cloud') {
            steps {
                withCredentials([file(credentialsId: 'gcp-service-account', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    sh '''
                    gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS
                    gcloud config set project $GCP_PROJECT_ID
                    gcloud auth configure-docker $GCP_REGION-docker.pkg.dev
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $GCP_REGION-docker.pkg.dev/$GCP_PROJECT_ID/$REPO_NAME/$IMAGE_NAME:latest .'
            }
        }

        stage('Push to Google Artifact Registry') {
            steps {
                sh 'docker push $GCP_REGION-docker.pkg.dev/$GCP_PROJECT_ID/$REPO_NAME/$IMAGE_NAME:latest'
            }
        }
    }
}
