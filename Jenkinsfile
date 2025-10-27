pipeline {
    agent any

    environment {
        IMAGE_NAME = "satyalasya/sample-repo"
        IMAGE_TAG = "kubeimage1"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                echo "🔨 Building Docker Image..."
                bat "docker build -t kubdemoapp:v1 ."
            }
        }

        stage('Docker Login') {
            steps {
                echo "🔐 Logging into Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat """
                        echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                    """
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                echo "📦 Tagging and pushing Docker image to Docker Hub..."
                bat """
                    docker tag kubdemoapp:v1 %IMAGE_NAME%:%IMAGE_TAG%
                    docker push %IMAGE_NAME%:%IMAGE_TAG%
                """
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "🚀 Deploying to Kubernetes..."
                bat """
                    kubectl apply -f deployment.yaml --validate=false
                    kubectl apply -f service.yaml
                """
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Please check the logs.'
        }
    }
}
