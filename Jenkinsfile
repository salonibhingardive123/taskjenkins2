

pipeline {
    agent any

    environment {
        ACR_NAME = "SBCRjenkins"
        IMAGE_NAME = "myapp"
        RESOURCE_GROUP = "SBJK11"
        APP_NAME = "jenkinstask11app"
    }

    stages {

        
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Login to ACR') {
            steps {
                sh 'az acr login --name $ACR_NAME'
            }
        }

        stage('Tag Image') {
            steps {
                sh 'docker tag $IMAGE_NAME:latest $ACR_NAME.azurecr.io/$IMAGE_NAME:latest'
            }
        }

        stage('Push Image to ACR') {
            steps {
                sh 'docker push $ACR_NAME.azurecr.io/$IMAGE_NAME:latest'
            }
        }

        stage('Deploy to App Service') {
            steps {
                sh '''
                az webapp config container set \
                --name $APP_NAME \
                --resource-group $RESOURCE_GROUP \
                --docker-custom-image-name $ACR_NAME.azurecr.io/$IMAGE_NAME:latest
                '''
            }
        }
    }
}
