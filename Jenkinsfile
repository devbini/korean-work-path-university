pipeline {
    agent any

    stages {
        // Front 빌드 단계
        stage('Build Front') {
            steps {
                script {
                    timeout(time: 10, unit: 'MINUTES') {
                        sh 'docker-compose build frontend'
                    }
                }
            }
        }

        // Back 빌드 단계
        stage('Build Back') {
            steps {
                script {
                    sh 'docker-compose build backend'
                }
            }
        }

        // Front 배포 단계 🚀
        stage('Deploy Front') {
            steps {
                script {
                    try {
                        def frontendRunning = sh(script: 'docker-compose ps -q frontend', returnStdout: true).trim()
                        if (frontendRunning) {
                            echo "Front Service 찾음! 삭제를 진행합니다."
                            sh 'docker-compose stop frontend'
                            sh 'docker-compose rm -f frontend'
                        }
                    } catch (Exception e) {
                        echo "Front-end Service not found !"
                    }
 
                    echo "Front Service 시작! 🚀"
                    sh 'docker-compose up --build -d frontend'
                }
            }
        }

        // Back 배포 단계 🚀
        stage('Deploy Back') {
            steps {
                script {
                    try {
                        def backendRunning = sh(script: 'docker-compose ps -q backend', returnStdout: true).trim()
                        if (backendRunning) {
                            echo "Back Service 찾음! 삭제를 진행합니다."
                            sh 'docker-compose stop backend'
                            sh 'docker-compose rm -f backend'
                        }
                    } catch (Exception e) {
                        echo "Back-end Service not found !"
                    }

                    echo "Back Service 시작! 🚀"
                    sh 'docker-compose up --build -d backend'
                }
            }
        }

        // Nginx 재시작 단계 (Docker-In-Docker)
        stage('Restart Nginx') {
            steps {
                script {
                    sh 'service nginx restart'
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up temporary files!'
            sh 'docker image prune -f'
        }
        success {
            echo 'Deployment was successful!'
        }
        failure {
            echo 'Deployment failed...'
        }
    }
}