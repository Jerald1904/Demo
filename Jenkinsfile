pipeline {
    agent any
    tools {
        maven 'Maven-3.9'   // Name from Jenkins Global Tool Config
        jdk 'jdk-17'        // Ensure JDK is also configured
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Jerald1904/App.git'
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
                        docker build -t jerald1904/app:${env.BUILD_NUMBER} .
                        docker tag jerald1904/app:${env.BUILD_NUMBER} jerald1904/app:latest
                    """
                }
            }
        }
        stage('Push to DockerHub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh """
                            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                            docker push jerald1904/app:${env.BUILD_NUMBER}
                            docker push jerald1904/app:latest
                        """
                    }
                }
            }
        }
    }
}

