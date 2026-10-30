
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Downloading TechFusion source code...'
                checkout scm
            }
        }

        stage('Validate') {
            steps {
                echo 'Checking project files...'
                sh '''
                    test -s index.html
                    test -s style.css
                    test -s script.js
                    test -s Dockerfile
                    test -s k8s/deployment.yaml
                    test -s k8s/service.yaml
                '''
                echo 'All required files are present!'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building TechFusion Docker image...'
                sh '''
                    set -eu
                    sudo -H -u ubuntu /usr/bin/minikube image build \
                        -t techfusion:${BUILD_NUMBER} .
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying TechFusion to Kubernetes...'
                sh '''
                    set -eu

                    sed "s|image: techfusion:latest|image: techfusion:${BUILD_NUMBER}|" \
                        k8s/deployment.yaml > deployment-ci.yaml

                    sudo -H -u ubuntu /usr/local/bin/kubectl apply \
                        -f deployment-ci.yaml

                    sudo -H -u ubuntu /usr/local/bin/kubectl apply \
                        -f k8s/service.yaml

                    sudo -H -u ubuntu /usr/local/bin/kubectl rollout status \
                        deployment/techfusion-deployment --timeout=180s

                    sudo -H -u ubuntu /usr/local/bin/kubectl get pods

                    sudo -H -u ubuntu /usr/local/bin/kubectl get services
                '''
            }
        }
    }

    post {
        success {
            echo 'TechFusion successfully deployed to Kubernetes!'
        }

        failure {
            echo 'TechFusion CI/CD Pipeline failed!'
        }
    }
}
