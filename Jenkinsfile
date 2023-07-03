pipeline {
    agent any
    environment {
        AWS_DEFAULT_REGION = 'ap-northeast-1'
        KUBECONFIG_ID = 'kubeid'
    }
    stages {
        stage('Build Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'awsid', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh '''
                     aws ecr get-login-password --region ap-northeast-1 | docker login --username AWS --password-stdin 563508789483.dkr.ecr.ap-northeast-1.amazonaws.com
                     docker build -t nodejs .
                     docker tag nodejs:latest 563508789483.dkr.ecr.ap-northeast-1.amazonaws.com/nodejs:latest
                     docker push 563508789483.dkr.ecr.ap-northeast-1.amazonaws.com/nodejs:latest
                    '''
                }
            }
        }
        stage('Deploy to EKS') {
            steps {
                withAWS(credentials: 'awsid') {
                    withCredentials([file(credentialsId: "${KUBECONFIG_ID}", variable: 'kubeid')]) {
                        sudo minikube start --driver=docker --force

                   sh "kubectl delete deployment.apps/deployment-204890 -n game-204873"
                        sh "kubectl delete service/service-204891 -n game-204873"
                    
                        sh "kubectl apply -f deployment.yaml"
                        sh "kubectl apply -f ingress.yaml"
                    }
                }
            }
        }
    }
}
