pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/GeraldAkenji/devops-nodejs-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("devops-nodejs-app:${env.BUILD_ID}")
                }
            }
        }

        stage('Security Scan') {
            steps {
                // Use Trivy to scan the Docker image for vulnerabilities
                sh 'docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy:latest image devops-nodejs-app:${env.BUILD_ID}'
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'd0c52560-0c6e-4ac6-9953-550e905586ea') {
                        docker.image("devops-nodejs-app:${env.BUILD_ID}").push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                kubernetesDeploy(kubeconfigId: 'kubeconfig', configs: 'k8s/deployment.yaml', enableConfigSubstitution: true)
            }
        }
    }
}
