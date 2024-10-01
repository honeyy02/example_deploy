pipeline {
    agent any

    environment {
        KUBECONFIG = "${WORKSPACE}/kubeconfig"
    }

    stages {
        stage('checkout') {
            steps {
                // Clone your repository where the nginx-deployment.yaml is stored
                checkout scm
            }
        }

        stage('Configure Kubeconfig') {
            steps {
                script {
                    // Access the K3s token stored in Jenkins credentials
                    withCredentials([string(credentialsId: 'k3s_credentials', variable: 'KUBE_TOKEN')]) {
                        // Create a kubeconfig file using the correct server address and token
                        writeFile file: 'kubeconfig', text: """
                        apiVersion: v1
                        clusters:
                        - cluster:
                            server: https://192.168.56.101:6443  # Use the correct IP address
                            certificate-authority-data: DATA+OMITTED
                          name: my-cluster
                        contexts:
                        - context:
                            cluster: my-cluster
                            user: jenkins
                          name: my-context
                        current-context: my-context
                        kind: Config
                        preferences: {}
                        users:
                        - name: jenkins
                          user:
                            token: ${KUBE_TOKEN}  # Use the token from credentials
                        """
                        sh 'chmod 600 kubeconfig' // Set appropriate permissions
                    }
                }
            }
        }

        stage('Deploy Nginx') {
            steps {
                script {
                    // Apply the Kubernetes deployment
                    sh 'kubectl --kubeconfig=kubeconfig apply -f nginx-deployment.yaml'
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    // Check if Nginx pods are running
                    sh 'kubectl --kubeconfig=kubeconfig rollout status deployment/nginx-deployment'
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
