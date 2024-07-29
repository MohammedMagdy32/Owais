pipeline {
    agent {
        docker {
            image 'node:14'
        }
    }

    environment {
        registry = "mohammed32/owais"
        dockerImage = ''
    }

    stages {
        stage('Clone Repository') {
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
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('') {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Deploy to Cloud') {
            steps {
                // Example for AWS ECS
                withAWS(credentials: 'aws-credentials', region: 'us-west-2') {
                    sh 'aws ecs update-service --cluster your-cluster-name --service your-service-name --force-new-deployment'
                }
                // Example for Kubernetes
                // kubernetesDeploy configs: 'k8s-deployment.yaml', kubeconfigId: 'kubeconfig'
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

