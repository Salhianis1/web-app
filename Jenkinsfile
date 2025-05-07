

pipeline {
    agent any

    environment {
        registry = 'salhianis20/web-app'
        registryCredential = 'dockerhub-id'
        dockerImage = 'web-app'
    }

    stages {

        stage('Clone Repo') {
            steps {
                git url: 'https://github.com/Salhianis1/web-app.git', branch: 'master'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${registry}:${BUILD_NUMBER}")
                }
            }
        }

/*        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    // Login to Docker Hub
                    docker.withRegistry('', registryCredential) {
                        // Push the Docker image to the registry
                        dockerImage.push()
                    }
                }
            }
        }*/
        
stage('Push Docker Image to Docker Hub') {
    steps {
        script {
            // Fetch Docker credentials from Vault
            stages {
        stage('Login to Docker Registry') {
            steps {
                script {
                    // Retrieve Docker credentials from Vault
                    withVault([vaultSecrets: [[path: "${env.VAULT_SECRET_PATH}", secretValues: [
                        [envVar: 'DOCKER_USERNAME', vaultKey: 'username'],
                        [envVar: 'DOCKER_PASSWORD', vaultKey: 'password']
                    ]]]]) {
                        // Use the retrieved credentials in the docker.withRegistry block
                        docker.withRegistry('https://registry.hub.docker.com', "${DOCKER_USERNAME}:${DOCKER_PASSWORD}") {
                            // Docker build or pull commands
                            docker.build('my-image')
                        }
                    }
                }
            }
        }
    }
        }
    }
}



        stage('Run Container') {
            steps {
                script {
                    // Stop and remove any running container with the same name, then run the new one
                    sh """
                        docker rm -f web-app-container || true
                        docker run -d --name web-app-container -p 8084:80 ${registry}:${BUILD_NUMBER}
                    """
                }
            }
        }
    }
}
