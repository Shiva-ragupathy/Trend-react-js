pipeline {
    agent any

    environment {
        IMAGE = "shiva-ragupathy/trend-app"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/Shiva-ragupathy/Trend-react-js.git',
                    credentialsId: 'github-credentials'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE:latest .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', 
                                                  usernameVariable: 'USER', 
                                                  passwordVariable: 'PASS')]) {
                    sh '''
                        echo $PASS | docker login -u $USER --password-stdin
                        docker push $IMAGE:latest
                    '''
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                    aws eks update-kubeconfig --name devops-cluster --region ap-south-1
                    kubectl apply -f k8s/deployment.yaml
                    kubectl apply -f k8s/service.yaml
                '''
            }
        }
    }
}
