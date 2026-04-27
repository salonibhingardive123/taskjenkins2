pipeline {
    agent any

    environment {
        ACR_NAME = "SBCRjenkins"
        ACR_LOGIN_SERVER = "sbcrjenkins-dfe6amhkb8f0abhq.azurecr.io"
        IMAGE_NAME = "myapp"
        RESOURCE_GROUP = "SBJK11"
        APP_NAME = "jenkinstask11app"

        // Service Principal details
        ARM_CLIENT_ID = credentials('azure-client-id')
        ARM_CLIENT_SECRET = credentials('azure-client-secret')
        ARM_TENANT_ID = credentials('azure-tenant-id')
        ARM_SUBSCRIPTION_ID = credentials('azure-subscription-id')
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Azure Login (Service Principal)') {
            steps {
                sh '''
                az login --service-principal \
                -u $AZURE_CLIENT_ID \
                -p $AZURE_CLIENT_SECRET \
                --tenant $AZURE_TENANT_ID
                '''
            }
        }

        stage('Login to ACR') {
            steps {
                sh 'az acr login --name $ACR_NAME'
            }
        }

        stage('Tag Image') {
            steps {
                sh 'docker tag $IMAGE_NAME:latest $ACR_LOGIN_SERVER/$IMAGE_NAME:latest'
            }
        }

        stage('Push Image to ACR') {
            steps {
                sh 'docker push $ACR_LOGIN_SERVER/$IMAGE_NAME:latest'
            }
        }

        stage('Deploy to App Service') {
            steps {
                sh '''
                az webapp config container set \
                --name $APP_NAME \
                --resource-group $RESOURCE_GROUP \
                --docker-custom-image-name $ACR_LOGIN_SERVER/$IMAGE_NAME:latest
                '''
            }
        }
    }
}


