pipeline {
    agent any
    
    tools { 
        nodejs "NodeJS" 
    }
    
    environment {
        registry = "mohammed32/owais"
        dockerImage = 'assessment'
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
                    dockerImage.push()
                    dockerImage.push('latest')
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
