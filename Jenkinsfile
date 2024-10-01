pipeline {
    agent any

    environment {
        KUBECONFIG_CREDENTIALS_ID = 'k3s_credentials' // The secret ID for the kubeconfig file
        K3S_NAMESPACE = 'jenkins' // Change to your desired namespace
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Use withCredentials to handle the secret file for kubeconfig
                    withCredentials([file(credentialsId: KUBECONFIG_CREDENTIALS_ID, variable: 'KUBECONFIG')]) {
                        // Apply the Kubernetes deployment and service manifest using the kubeconfig
                        sh "kubectl apply -f deployment.yaml -n ${K3S_NAMESPACE}"
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'NGINX deployment completed successfully!'
        }
        failure {
            echo 'NGINX deployment failed.'
        }
    }
}
