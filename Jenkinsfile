pipeline {
    agent any

    tools {
        jdk 'jdk-21'
        maven 'maven-3'
    }

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {

        stage('Build') {
            steps {
                bat 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test'
            }
        }

        stage('Package') {
            steps {
                bat 'mvn package -DskipTests'
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        // ---------------- CD STAGES START HERE ----------------

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t shopping-cart-app:latest .'
            }
        }

        stage('Run Docker Container') {
            steps {
                bat '''
                docker stop shopping-cart || exit 0
                docker rm shopping-cart || exit 0
                docker run -d -p 8080:8080 --name shopping-cart shopping-cart-app:latest
                '''
            }
        }

        // ---------------- CD STAGES END HERE ----------------
    }

    post {
        success {
            echo 'CI/CD Pipeline SUCCESS'
        }
        failure {
            echo 'CI/CD Pipeline FAILED'
        }
        always {
            cleanWs()
        }
    }
}
