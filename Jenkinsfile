pipeline {
    agent any

    environment {
        KUBECONFIG = credentials('udhaya-microk8s-config')
        DOCKER_CREDENTIALS_ID = 'Docker-udhaya21'
        DOCKER_IMAGE = 'udhaya21/jenkins-udhaya'
        K8S_NAMESPACE = 'default'
    }
    options {
        retry(3) // Retry stages 3 times if they fail
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    git credentialsId: 'Github-udhaya1148', url: 'https://github.com/udhaya1148/Jenkins.git', branch: 'main'
                }
                // Checkout your application code and Kubernetes manifests from your repository
               // git 'https://github.com/udhaya1148/Jenkins.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build and push Docker image
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh """
                        docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
                        docker build -t $DOCKER_IMAGE .
                        docker push $DOCKER_IMAGE
                        """
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Set the kubeconfig environment variable
                    sh 'echo $KUBECONFIG > kubeconfig'
                    sh 'export KUBECONFIG=kubeconfig'

                    // Apply Kubernetes manifests
                    sh 'kubectl create namespace jenkins-udhaya'
                    sh 'kubectl apply -f k8s/ -n jenkins-udhaya'
                }
            }
        }
    }

    post {
        always {
            // Clean up
            sh 'rm -f kubeconfig'
        }
    }
}
