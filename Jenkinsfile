pipeline {
    agent any
    tools {
        maven 'Maven-3.9'   // Jenkins tool name for Maven
        jdk 'jdk-17'        // Jenkins tool name for JDK
    }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub') // Jenkins credentials ID
        IMAGE_NAME = "your-dockerhub-username/demoapp"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your-repo.git'
            }
        }
        stage('Build JAR') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                        docker build -t $IMAGE_NAME:${env.BUILD_NUMBER} .
                        docker tag $IMAGE_NAME:${env.BUILD_NUMBER} $IMAGE_NAME:latest
                    """
                }
            }
        }
        stage('Push to DockerHub') {
            steps {
                script {
                    sh """
                        echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                        docker push $IMAGE_NAME:${env.BUILD_NUMBER}
                        docker push $IMAGE_NAME:latest
                    """
                }
            }
        }
    }
    post {
        success {
            echo "✅ Image pushed successfully: $IMAGE_NAME:${env.BUILD_NUMBER}"
        }
    }
}

