pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven3'
    }

    environment {
        SCANNER_HOME = tool 'sonarqube-scanner'
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Akash05061/fullstack-Blogging-App.git'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Trivy FS Scan') {
            steps {
                sh 'trivy fs --format table -o trivy-fs.html .'
            }
        }

        stage('Static Code Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh """
                       $SCANNER_HOME/bin/sonar-scanner \
                       -Dsonar.projectName=blogapp \
                       -Dsonar.projectKey=blogapp \
                       -Dsonar.java.binaries=target
                    """
                }
            }
        }

        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh 'docker build -t akash501/blogapplication:latest .'
                    }
                }
            }
        }

        stage('Trivy Image Scan') {
            steps {
                sh 'trivy image --format table -o trivy-image.html akash501/blogapplication:latest'
            }
        }

        stage('Docker Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh 'docker push akash501/blogapplication:latest'
                    }
                }
            }
        }
    }
}
