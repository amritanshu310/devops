pipeline {
    agent any

    tools {
        maven 'Maven3.9.9'
        jdk 'JDK17'
        dockerTool 'Docker Desktop'
    }

    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN')
        DOCKER_CREDENTIALS = credentials('DOCKER_CREDENTIALS')
        PATH = "/opt/homebrew/bin:/usr/local/bin:/usr/bin:/bin:$PATH"
        DOCKER_HOST = "unix:///Users/amritanshu/.docker/run/docker.sock"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Set Version') {
            steps {
                script {
                    env.PROJECT_VERSION = sh(script: 'mvn help:evaluate -Dexpression=project.version -q -DforceStdout', returnStdout: true).trim()
                    echo "Project version: ${env.PROJECT_VERSION}"
                }
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    sh """
                    mvn sonar:sonar \
                        -Dsonar.projectKey=hello-world \
                        -Dsonar.projectName='hello-world' \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.token=${SONAR_TOKEN}
                    """
                }
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    sh """
                    mvn com.google.cloud.tools:jib-maven-plugin:3.3.1:build \
                        -Djib.to.auth.username=${DOCKER_CREDENTIALS_USR} \
                        -Djib.to.auth.password=${DOCKER_CREDENTIALS_PSW} \
                        -Dimage=amritanshu310/hello-world:${env.PROJECT_VERSION}
                    """
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo "Project version: ${env.PROJECT_VERSION}"
                    echo "Current PATH: $PATH"
                    echo "DOCKER_HOST: $DOCKER_HOST"
                    
                    sh "which docker"
                    sh "docker --version"
                    sh "docker info"
                    
                    sh "docker pull amritanshu310/hello-world:${env.PROJECT_VERSION}"
                    sh "docker stop hello-world-prod || true"
                    sh "docker rm hello-world-prod || true"
                    sh "docker run -d --name hello-world-prod -p 80:8080 amritanshu310/hello-world:${env.PROJECT_VERSION}"
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished'
            deleteDir() 
        }
        success {
            echo 'Pipeline succeeded!'
            mail to: 'amritanshucodes@gmail.com',
                 subject: "Success: ${currentBuild.fullDisplayName}",
                 body: "The pipeline ${currentBuild.fullDisplayName} has completed successfully."
        }
        failure {
            echo 'Pipeline failed!'
            mail to: 'amritanshucodes@gmail.com',
                 subject: "Failed: ${currentBuild.fullDisplayName}",
                 body: "The pipeline ${currentBuild.fullDisplayName} has failed. Please check the console output for details."
        }
    }
}