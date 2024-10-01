pipeline {
    agent any

    environment {
        KUBECONFIG_CREDENTIALS_ID = 'k3s_credentials' // Jenkins credentials ID for kubeconfig
        K8S_NAMESPACE = 'jenkins' // Change to your desired namespace
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
                    // Use withCredentials to handle the secret text for kubeconfig
                    withCredentials([string(credentialsId: KUBECONFIG_CREDENTIALS_ID, variable: 'KUBECONFIG_CONTENT')]) {
                        // Create a temporary kubeconfig file with the content of the secret
                        writeFile file: 'kubeconfig', text: "${KUBECONFIG_CONTENT}"
                        
                        // Apply the Kubernetes deployment and service manifest using the temporary kubeconfig
                        sh "KUBECONFIG=./kubeconfig kubectl apply -f deployment.yaml -n ${K8S_NAMESPACE}"
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
