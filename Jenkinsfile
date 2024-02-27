pipeline {
    agent none // Esto permite definir agentes específicos para cada etapa
    // tools {
    //     sonarQube 'sonarscanner'
    // }
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18.19.1-alpine' // Usa una imagen de Docker con Node.js para construir el proyecto Angular
                    // args '-v /var/run/docker.sock:/var/run/docker.sock' // Permite a Docker comunicarse con el daemon de Docker
                    args '-u root'
                }
            }
            steps {
                script {
                    // Instala dependencias y construye el proyecto
                    sh 'npm install'
                    sh 'npm run build'
                    sh 'npm install -g sonarqube-scanner'
                    // Ejecuta el análisis de SonarQube
                    // Asegúrate de reemplazar YOUR_SONAR_HOST y YOUR_SONAR_TOKEN con tus propios valores
                    // sh "sonar-scanner \
                    //     -Dsonar.projectKey=spring-petclinic-angular \
                    //     -Dsonar.sources=. \
                    //     -Dsonar.host.url=YOUR_SONAR_HOST \
                    //     -Dsonar.login=YOUR_SONAR_TOKEN"
                    // sh 'sonar-scanner --version'
                    sh 'env | sort'
                    def scannerHome = tool 'sonarscanner';
                    withSonarQubeEnv('sonarqube') { // If you have configured more than one global server connection, you can specify its name
                        //sh "ls -la $scannerHome/bin/"
                        
                        //sh "${scannerHome}/bin/sonar-scanner"
                        sh "sonar-scanner --version"
                    }
                }
            }
        }
        // stage('Build and SonarQube Analysis') {
        //     agent {
        //         docker {
        //             image 'sonarsource/sonar-scanner-cli:5.0.1'
        //         }
        //     }
        //     steps {
        //         script {
        //             sh 'sonar-scanner --version'

        //             withSonarQubeEnv('sonarqube') { // If you have configured more than one global server connection, you can specify its name
        //                 sh 'env | sort'
        //                 sh "sonar-scanner --version"
        //                 sh "sonar-scanner --help"
        //                 sh "sonar-scanner"
        //             }
        //         }
        //     }
        // }        
    }
}