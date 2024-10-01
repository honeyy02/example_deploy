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
                        def caData = "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJlRENDQVIyZ0F3SUJBZ0lCQURBS0JnZ3Foa2pPUFFRREFqQWpNU0V3SHdZRFZRUUREQmhyTTNNdGMyVnkKZG1WeUxXTmhRREUzTWpjeE5UUXlOemt3SGhjTk1qUXdPVEkwTURVd05ETTVXaGNOTXpRd09USXlNRFV3TkRNNQpXakFqTVNFd0h3WURWUVFEREJock0zTXRjMlZ5ZG1WeUxXTmhRREUzTWpjeE5UUXlOemt3V1RBVEJnY3Foa2pPClBRSUJCZ2dxaGtqT1BRTUJCd05DQUFTZDhQRFp5b3VUNlBpZy9XaEFpMW5JRi85VnZ2ODZKY3ozcHRJWFRMV3IKZCsyeXQ2VFVzRDYzZXI3ZzhsMVBsQVphVUczQ3pHSUF6RXV3WWVlc0g0eDFvMEl3UURBT0JnTlZIUThCQWY4RQpCQU1DQXFRd0R3WURWUjBUQVFIL0JBVXdBd0VCL3pBZEJnTlZIUTRFRmdRVTQ2THYrOFhTdmswRGl5Uzd1QVZwCitYaW1qU2t3Q2dZSUtvWkl6ajBFQXdJRFNRQXdSZ0loQUxtSUhBZkliOTdCb0NiZlhFM3ZmdzJZZWJZOUZldVUKTUgrT25NMUtjWnZnQWlFQWkxV2ErNCtIdGMxakx1YXJvVXlKQTFjVEdCdkFZaUNONkJwZFVyeFBPWk09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
                        writeFile file: 'kubeconfig', text: """
                        apiVersion: v1
                        clusters:
                        - cluster:
                            server: https://192.168.56.101:6443  # Use the correct IP address
                            certificate-authority-data: ${caData}
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
                    sh 'kubectl --kubeconfig=kubeconfig apply -f deployment.yaml --validate=false'
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    // Check if Nginx pods are running
                    sh 'kubectl --kubeconfig=kubeconfig rollout status deployment'
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
