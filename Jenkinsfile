pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/priyashukla1411/eks-loadbalancer.git']]])
            }
        }
  
        stage('Docker Image Build') {
            steps {
                sh 'docker build -t nodejs .'
            }
        }
        stage('Push Docker Image to ECR') {
            steps {
                withAWS(credentials: 'AKIAYGM55YDV54HJTQG5', region: 'ap-northeast-1') {
                    sh 'aws ecr get-login-password --region ap-northeast-1 | docker login --username AWS --password-stdin 563508789483.dkr.ecr.ap-northeast-1.amazonaws.com'
                    sh 'docker tag nodejs:latest 563508789483.dkr.ecr.ap-northeast-1.amazonaws.com/nodejs:latest'
                    sh 'docker push 563508789483.dkr.ecr.ap-northeast-1.amazonaws.com/nodejs:latest'
                }
            }
        }
        stage('Integrate Jenkins with EKS Cluster and Deploy App') {
            steps {
               withAWS(credentials: 'AKIAYGM55YDV54HJTQG5', region: 'ap-northeast-1') {
                  script {
                    sh ('aws eks --region ap-northeast-1 update-kubeconfig  --name eks-cluster')
                    sh "kubectl apply -f deployment.yaml"
                }
                }
        }
    }
    }
}
