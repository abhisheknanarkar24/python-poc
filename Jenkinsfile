pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '**']], extensions: [], userRemoteConfigs: [[credentialsId: 'GitHub', url: 'https://github.com/abhisheknanarkar24/python-poc.git']]])
            }
        }
        stage('Build Docker Image') {
            steps {
               sh 'docker build -t python-poc .'
               
            }
        }
        stage('push docker image to ECR') {
            steps {
                withAWS(credentials: 'abhishek_aws', endpointUrl: 'https://294426219574.signin.aws.amazon.com/', region: 'us-east-1') {
                    sh '''/usr/local/bin/aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 294426219574.dkr.ecr.us-east-1.amazonaws.com
                    docker tag python-poc:latest 294426219574.dkr.ecr.us-east-1.amazonaws.com/app:$
                    docker push 294426219574.dkr.ecr.us-east-1.amazonaws.com/app:python-poc'''
                    }
             
            }
        }
        stage('Lambda Deployment') {
            steps {
               withAWS(credentials: 'abhishek_aws', endpointUrl: 'https://294426219574.signin.aws.amazon.com/', region: 'us-east-1') {
               sh '/usr/local/bin/aws lambda update-function-code --region us-east-1 --function-name app-poc  --image-uri 294426219574.dkr.ecr.us-east-1.amazonaws.com/app:python-poc'
                            }
            }
            
    }}}