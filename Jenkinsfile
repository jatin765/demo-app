pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "jatin765/pipeline"
        BUILD_TAG = "${DOCKER_IMAGE}:${BUILD_NUMBER}"
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout from Git') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/jatin765/demo-app.git']])
            }
        }

        stage('Build Perform') {
            steps {
                echo "Build is Successful"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh "docker build -t ${BUILD_TAG} --no-cache ."
                    }
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh "docker push ${BUILD_TAG}"
                    }
                }
            }
        }

        stage('Update Deployment Manifest') {
            steps {
                script {
                    sh "sed -i 's|image: ${DOCKER_IMAGE}:old_tag|image: ${BUILD_TAG}|' deployment.yml"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withKubeConfig(credentialsId: 'kube-config', serverUrl: 'https://625E772E8628B69127F59643BE33535B.gr7.ap-south-1.eks.amazonaws.com') {
                        sh 'kubectl apply -f deployment.yml'
                    }
                }
            }
        }
    }
}
