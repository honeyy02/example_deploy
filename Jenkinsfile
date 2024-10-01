pipeline {
    agent any

    environment {
        KUBECONFIG = "${WORKSPACE}/kubeconfig"
    }

    stages {
        stage('Checkout') {
            steps {
                // Clone your repository where the nginx-deployment.yaml is stored
               checkout scm
            }
        }

        stage('Configure Kubeconfig') {
            steps {
                script {
                    // Create a kubeconfig file using the token
                    def k3sToken = credentials('k3s_credentials')
                    writeFile file: 'kubeconfig', text: """
                    apiVersion: v1
                    clusters:
                    - cluster:
                        server: https://192.168.56.101:6443
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
                        token: ${k3sToken}
                    """
                    sh 'chmod 600 kubeconfig' // Set appropriate permissions
                }
            }
        }

        stage('Deploy Nginx') {
            steps {
                script {
                    // Apply the Kubernetes deployment
                    sh 'kubectl --kubeconfig=kubeconfig apply -f deployment.yaml'
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
