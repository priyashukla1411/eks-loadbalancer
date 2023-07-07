pipeline {
    agent any
    environment {
        AWS_DEFAULT_REGION ='ap-northeast-1'
        KUBECONFIG_ID ='kubeid'
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
             environment {
                 
                 
                    // Set AWS credentials
                    AWS_ACCESS_KEY_ID = credentials('AKIAYGM55YDV54HJTQG5')
                    AWS_SECRET_ACCESS_KEY = credentials('Xt/QBQyC8TQ+QXz190oi+ljcMS4KWSzXtNOoTLwU')
             steps {
                    withAWS(region: 'your-aws-region', credentials: 'aws-access-key-id') {
                    sh 'kubectl apply -f deployment.yaml'
                    }
                }
            }
        }
    }
}
