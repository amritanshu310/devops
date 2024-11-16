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
                sh 'mvn clean package -q' 
                echo "Build completed."
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test -q' 
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
                    sh "docker build -t amritanshu310/hello-world:${env.PROJECT_VERSION} ."
                    echo "Docker image built."

                    withCredentials([usernamePassword(credentialsId: 'DOCKER_CREDENTIALS', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh """
                        echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                        docker push amritanshu310/hello-world:${env.PROJECT_VERSION}
                        """
                    }
                    echo "Docker image pushed successfully."
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh "docker pull amritanshu310/hello-world:${env.PROJECT_VERSION}"
                    sh "docker stop hello-world-prod || true"
                    sh "docker rm hello-world-prod || true"
                    sh "docker run -d --name hello-world-prod -p 80:8080 amritanshu310/hello-world:${env.PROJECT_VERSION}"
                    echo "Deployment completed."
                }
            }
        }
    }

    post {
        always {
            deleteDir() // Clean up workspace
        }
        success {
            mail to: 'amritanshucodes@gmail.com',
                 subject: "Pipeline Success: ${currentBuild.fullDisplayName}",
                 body: "The pipeline ${currentBuild.fullDisplayName} completed successfully."
        }
        failure {
            mail to: 'amritanshucodes@gmail.com',
                 subject: "Pipeline Failure: ${currentBuild.fullDisplayName}",
                 body: "The pipeline ${currentBuild.fullDisplayName} failed. Check the logs for details."
        }
    }
}
