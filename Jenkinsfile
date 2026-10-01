pipeline {
    agent any

    environment {
        IMAGE_NAME = 'devops-web'
        CONTAINER_NAME = 'devops-web-container'
        PORT = '8082'
    }

    stages {
        stage('Checkout Source') {
            steps {
                echo 'Checking out source code from SCM...'
                checkout scm
            }
        }

        stage('Validate Application') {
            steps {
                echo 'Validating repository structure and required files...'
                script {
                    if (!fileExists('Dockerfile') || !fileExists('app/index.html')) {
                        error 'Required project files (Dockerfile or app/index.html) are missing!'
                    }
                    echo 'Validation successful: Dockerfile and app/index.html are present.'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker Image: ${IMAGE_NAME}..."
                script {
                    if (isUnix()) {
                        sh "docker build -t ${IMAGE_NAME} ."
                    } else {
                        bat "docker build -t ${IMAGE_NAME} ."
                    }
                }
            }
        }

        stage('Cleanup Old Container') {
            steps {
                echo "Stopping and removing existing container (${CONTAINER_NAME}) if active..."
                script {
                    if (isUnix()) {
                        sh "docker stop ${CONTAINER_NAME} || true"
                        sh "docker rm ${CONTAINER_NAME} || true"
                    } else {
                        bat "docker stop ${CONTAINER_NAME} || echo Container not running"
                        bat "docker rm ${CONTAINER_NAME} || echo Container not found"
                    }
                }
            }
        }

        stage('Deploy Docker Container') {
            steps {
                echo "Deploying container ${CONTAINER_NAME} on port ${PORT}..."
                script {
                    if (isUnix()) {
                        sh "docker run -d --name ${CONTAINER_NAME} -p ${PORT}:80 ${IMAGE_NAME}"
                    } else {
                        bat "docker run -d --name ${CONTAINER_NAME} -p ${PORT}:80 ${IMAGE_NAME}"
                    }
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                echo "Verifying application availability at http://localhost:${PORT}..."
                script {
                    if (isUnix()) {
                        sh "curl -s -o /dev/null -w '%{http_code}' http://localhost:${PORT} | grep 200"
                    } else {
                        bat "powershell -Command \"(Invoke-WebRequest -Uri 'http://localhost:${PORT}' -UseBasicParsing).StatusCode\""
                    }
                }
            }
        }

        stage('Deploy to Kubernetes (Optional)') {
            steps {
                script {
                    echo 'Attempting optional Kubernetes Deployment...'
                    try {
                        if (isUnix()) {
                            sh 'kubectl cluster-info && kubectl apply -f k8s/'
                        } else {
                            bat 'kubectl cluster-info && kubectl apply -f k8s/'
                        }
                    } catch (Exception e) {
                        echo "Kubernetes cluster not available or unreachable. Skipping K8s deployment stage: ${e.getMessage()}"
                    }
                }
            }
        }
    }

    post {
        success {
            echo "CI/CD Pipeline executed successfully! Application is live at http://localhost:${PORT}"
        }
        failure {
            echo "CI/CD Pipeline failed. Please check build logs for details."
        }
    }
}


