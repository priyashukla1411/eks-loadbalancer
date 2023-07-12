pipeline {
    agent any
    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
        KUBECONFIG_ID = 'kubeid'
    }
    stages {
        stage('Build Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'awsid', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh '''
                     
                     sudo aws ecr get-login-password --region us-east-1 |sudo  docker login --username AWS --password-stdin 329375589400.dkr.ecr.us-east-1.amazonaws.com
                     aws configure
                     sudo docker build -t nodejs .
                     sudo docker tag nodejs:latest 329375589400.dkr.ecr.us-east-1.amazonaws.com/nodejs:latest
                     sudo docker push 329375589400.dkr.ecr.us-east-1.amazonaws.com/nodejs:latest
                    '''
                }
            }
        }
        stage('Deploy to EKS') {
            steps {
                withAWS(credentials: 'awsid') {
                    withCredentials([file(credentialsId: "${KUBECONFIG_ID}", variable: 'kubeid')]) {
                   
                        sh "kubectl apply -f ingress.yaml"
                    }
                }
            }
        }
    }
}
