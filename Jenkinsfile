pipeline {
    agent any

    stages {
        stage('checkout') {
            steps {
                // Clone your repository where the nginx-deployment.yaml is stored
                checkout scm
            }
        }
        
        stage('Deploy Nginx') {
            steps {
                script {
                    // Apply the Kubernetes deployment
                    sh 'kubectl apply -f deployment.yaml'
                }
            }
        }
        
        stage('Verify Deployment') {
            steps {
                script {
                    // Check if Nginx pods are running
                    sh 'kubectl rollout status deployment'
                }
            }
        }
    }

    post {
        always {
            // Clean up the workspace
            cleanWs()
        }
        success {
            echo 'Nginx deployed successfully!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}
