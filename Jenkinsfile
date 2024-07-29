pipeline {
    agent any
    
    tools { 
        nodejs "NodeJS" 
    }
    
    environment {
        registry = "mohammed32/owais"
        dockerImage = ''
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
                    dockerImage = docker.build("${registry}:${env.BUILD_NUMBER}")
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Deploy to Cloud') {
            steps {
                script {
                    withAWS(credentials: 'aws-credentials', region: 'us-west-2') {
                        sh 'aws ecs update-service --cluster your-cluster-name --service your-service-name --force-new-deployment'
                    }
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

