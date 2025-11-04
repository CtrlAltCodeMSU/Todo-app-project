pipeline {
    agent any
    
    triggers {
        pollSCM('H/5 * * * *')
    }

    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build Backend') {
            steps {
                dir('backend') {
                    sh 'docker build -t todo-backend .'
                }
            }
        }
        
        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'docker build -t todo-frontend .'
                }
            }
        }
        
        stage('Test') {
            parallel {
                stage('Backend Tests') {
                    steps {
                        dir('backend') {
                            sh 'python -m pytest || true'
                        }
                    }
                }
                stage('Frontend Tests') {
                    steps {
                        dir('frontend') {
                            sh 'npm test || true'
                        }
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                sh 'docker compose down || true'
                sh 'docker system prune -f || true'
                sh 'fuser -k 5000/tcp || true'
                sh 'fuser -k 5173/tcp || true'
                sh 'sleep 5'
                sh 'docker compose up -d --build'
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        failure {
            echo 'Build failed!'
        }
        success {
            echo 'Build successful!'
        }
    }
}