pipeline {
    agent any

    tools { 
        nodejs "NodeJS" 
    }

    environment {
        registry = "mohammed32/owais"
        dockerImage = ''
        remoteServer = '157.175.4.250'
        sshCredentialsId = 'Deployment-Docker-Server'
    }

    stages {
        stage('Cloning Git') {
            steps {
                git branch: 'main', url: 'https://github.com/MohammedMagdy32/Owais'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${registry}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Security OWASP ZAP') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        sh "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://157.175.4.250:80/ || true"
                    }
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                sshagent(credentials: [sshCredentialsId]) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ubuntu@${remoteServer} '
                    cd /home/ubuntu/deploy &&
                    docker compose pull &&
                    docker compose up -d'
                    """
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}
