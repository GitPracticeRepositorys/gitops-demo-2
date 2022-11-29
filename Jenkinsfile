pipeline {

    environment {
        imageName = "limarktest/nodejs-docker"
    }
    agent any

    stages {

        stage('Checkout Code') {
            steps {
               checkout scm
            }
        }

        stage('Build & Tag Image') {
            steps {
                script {
                    sh 'docker build -t $imageName:$BUILD_NUMBER .'
                }
            }
        }

        stage('Remove Unused Docker Image') {
            steps{
                sh "docker rmi $imageName:$BUILD_NUMBER"
            }
        }
    }
}
