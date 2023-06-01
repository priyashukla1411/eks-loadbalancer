pipeline {
    agent any
    environment {
        AWS_DEFAULT_REGION = 'ap-northeast-1'
        KUBECONFIG_ID = '14f301b0-581d-4f00-9259-a2c61bd21c11'
    }
    stages {
        stage('Build Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: '563508789483', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
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
                withAWS(credentials: '	563508789483') {
                    withCredentials([file(credentialsId: "${KUBECONFIG_ID}", variable: 'KUBECONFIG')]) {

                        sh "kubectl delete deployment.apps/deployment-204890 -n game-204873"
                        sh "kubectl delete service/service-204891 -n game-204873"
                        sh "kubectl apply -f dep.yml"
                        sh "kubectl apply -f ingress.yml"
                    }
                }
            }
        }
    }
}