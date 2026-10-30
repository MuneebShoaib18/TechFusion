
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
                echo 'Checking website files...'

                sh 'test -s index.html'
                sh 'test -s style.css'
                sh 'test -s script.js'

                echo 'All website files are present!'
            }
        }

        stage('Build') {
            steps {
                echo 'Building TechFusion website...'

                sh 'rm -rf dist'
                sh 'mkdir -p dist'

                sh 'cp index.html style.css script.js dist/'

                echo 'Website build completed!'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying TechFusion to Nginx...'

                sh 'rsync -av --delete --exclude=.git --exclude=Jenkinsfile dist/ /var/www/html/'

                echo 'Deployment successful!'
            }
        }
    }

    post {
        success {
            echo 'TechFusion CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'TechFusion CI/CD Pipeline failed!'
        }
    }
}
