pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18.19.1-alpine'
                    // args '-u root'
                }
            }
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }
        stage('SonarQube') {
            agent {
                docker {
                    image 'sonarsource/sonar-scanner-cli:5.0.1'
                }
            }
            steps {
                script {
                    withSonarQubeEnv('sonarqube') {
                        // sh 'env | sort'
                        sh "sonar-scanner --version"
                        sh "sonar-scanner --help"
                        sh "sonar-scanner"
                    }
                }
            }
        }        
    }
}