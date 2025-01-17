pipeline {
    agent any
    environment {
        AWS_REGION = 'eu-west-2'
        EKS_CLUSTER_NAME = 'webapp-cluster'
        DOCKER_IMAGE = 'your-dockerhub-username/webapp:latest'
        KUBECONFIG = '/home/jenkins/.kube/config'
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/cloudboostauk/webApp-Quizz-B.git'
            }
        }
        stage('Terraform Deploy') {
            steps {
                sh '''
                terraform init
                terraform apply -auto-approve
                '''
            }
        }
        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $DOCKER_IMAGE .
                docker push $DOCKER_IMAGE
                '''
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f k8s-deployment.yaml
                '''
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
