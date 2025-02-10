pipeline {
    agent any

    environment {
        GCP_PROJECT_ID = 'voirophoenix-e6030'
        GCP_REGION = 'aisa-south1'
        REPO_NAME = 'voiro-dev'
        IMAGE_NAME = 'thinktank-image'
        SERVICE_ACCOUNT_CREDENTIALS = credentials('gcp-service-account')
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'VTECH-771-be-env-setup', credentialsId: 'nithingitcreds', url: 'https://github.com/anilkarat/thinktank.git'
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                withCredentials([file(credentialsId: 'gcp-service-account', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    sh '''
                    # Authenticate with Google Cloud
                    gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS
                    gcloud config set project $GCP_PROJECT_ID
                    # Build Docker Image
                    gsutil cp gs://thinktank-deployment/voot-replica.env thinktank_docker/voiro.env
                    docker compose -f thinktank_docker/docker-compose-dev.yml build --no-cache
                    # Push Docker Image
                    gcloud auth configure-docker $GCP_REGION-docker.pkg.dev                    
                    docker push $GCP_REGION-docker.pkg.dev/$GCP_PROJECT_ID/$REPO_NAME/$IMAGE_NAME:latest
                    '''
                }
            }
        }
    }
}
