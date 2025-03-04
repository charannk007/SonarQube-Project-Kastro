pipeline {
    agent any

    tools {
        maven 'maven'
        jdk 'java'
        // Remove the sonarQube line if it still causes issues
    }

    environment {
        SCANNER_HOME = tool 'sonar'
        DOCKERHUB_USERNAME = 'nkcharan'  
        DOCKER_IMAGE = "${DOCKERHUB_USERNAME}/spotify-app:latest"
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/KastroVKiran/SonarQube-Project-Kastro.git'
            }
        }

        stage('Compile') {
            steps {
                sh "mvn compile"
            }
        }

        stage('Test') {
            steps {
                sh "mvn test"
            }
        }

        stage('Sonar Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Kastro -Dsonar.projectKey=KastroKey -Dsonar.java.binaries=target"
                }
            }
        }

        stage('Build') {
            steps {
                sh "mvn package"
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    // Building Docker Image
                    sh "docker build -t $DOCKER_IMAGE ."
                }
            }
        }

        // stage('Docker Push to DockerHub') {
        //     steps {
        //         script {
        //                   sh "docker push $DOCKER_IMAGE"
        //             }
        //         }
        //     }
        

        stage('Run Docker Container') {
            steps {
                script {
                    // Stop any existing container with the same name
                    sh "docker stop spotify-app || true && docker rm spotify-app || true"
                    
                    // Running the container
                    sh "docker run -d --name spotify-app -p 5555:5555 $DOCKER_IMAGE"
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'
            cleanWs()  // Clean up the workspace after the build
        }
    }
}
