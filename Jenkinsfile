

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
            withVault(configuration: [
                disableChildPoliciesOverride: false,
                timeout: 60,
                vaultCredentialId: 'vault-cred', 
                vaultUrl: 'http://127.0.0.1:8200'
            ], vaultSecrets: [
                [path: 'secret/dockercred', secretValues: [
                    [vaultKey: 'username', envVar: 'DOCKER_USERNAME'], 
                    [vaultKey: 'pwd', envVar: 'DOCKER_PASSWORD']
                ]]
            ]) {
                // Login to Docker Hub using credentials retrieved from Vault
                docker.withRegistry('', [username: env.DOCKER_USERNAME, pwd: env.DOCKER_PASSWORD]) {
                    // Push the Docker image to the registry
                    dockerImage.push()
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
