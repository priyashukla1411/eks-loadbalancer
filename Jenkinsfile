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
                     aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 329375589400.dkr.ecr.us-east-1.amazonaws.com
                     docker build -t nodejs .
                     docker tag nodejs:latest 329375589400.dkr.ecr.us-east-1.amazonaws.com/nodejs:latest
                     docker push 329375589400.dkr.ecr.us-east-1.amazonaws.com/nodejs:latest
                    '''
                }
            }
        }
        stage('Deploy to EKS') {
            steps {
                withAWS(credentials: 'awsid') {
                    withCredentials([file(credentialsId: "${KUBECONFIG_ID}", variable: 'kubeid')]) {
                        sh "kubectl delete deployment.apps/deployment-2048100 -n game-204873"
                        sh "kubectl delete service/service-2048102 -n game-204873"
                        sh "kubectl apply -f HomeApp.yaml"
                        sh "kubectl apply -f Ingress.yaml"
                    }
                }
            }
        }
    }
}
