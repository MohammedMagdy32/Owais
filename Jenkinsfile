pipeline {
    agent any

    environment {
        registry = "mohammed32/owais"
        dockerImage = ''
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/MohammedMagdy32/Owais'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                script {
                    def nodeHome = tool name: 'NodeJS', type: 'NodeJSInstallation'
                    env.PATH = "${nodeHome}/bin:${env.PATH}"
                    sh 'npm install'
                }
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

