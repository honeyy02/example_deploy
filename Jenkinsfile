pipeline {
    agent {
        k3 {
            label 'nginx-pod'
            yaml """
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: kubectl
                image: bitnami/kubectl
                command:
                - cat
                tty: true
            """
        }
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Deploy NGINX to Kubernetes') {
            steps {
                container('kubectl') {
                    // Apply the Kubernetes deployment and service manifest
                    sh "kubectl apply -f deployment.yaml"
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
