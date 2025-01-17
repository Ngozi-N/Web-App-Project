pipeline {
    agent any
    environment {
        AWS_REGION = 'eu-west-2'
        EKS_CLUSTER_NAME = 'webapp-cluster'
        DOCKER_IMAGE = 'ngozin/webapp:latest'
        KUBECONFIG = '/home/jenkins/.kube/config'
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Ngozi-N/Web-App-Project.git'
            }
        }
        stage('Terraform Deploy') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'webapp',
                    accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                    secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                ]]) {
                    sh '''
                    terraform init
                    terraform apply -auto-approve
                    '''
                }
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
