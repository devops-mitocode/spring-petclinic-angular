pipeline {
    agent none // Esto permite definir agentes específicos para cada etapa

    stages {
        stage('Checkout') {
            agent any // Puede ejecutarse en cualquier agente
            steps {
                checkout scm // Hace checkout del código fuente desde el repositorio configurado en el proyecto de Jenkins
            }
        }

        stage('Build and SonarQube Analysis') {
            agent {
                docker {
                    image 'node:14-alpine' // Usa una imagen de Docker con Node.js para construir el proyecto Angular
                    args '-v /var/run/docker.sock:/var/run/docker.sock' // Permite a Docker comunicarse con el daemon de Docker
                }
            }
            steps {
                script {
                    // Instala dependencias y construye el proyecto
                    sh 'npm install'
                    sh 'npm run build'
                    // Ejecuta el análisis de SonarQube
                    // Asegúrate de reemplazar YOUR_SONAR_HOST y YOUR_SONAR_TOKEN con tus propios valores
                    sh "sonar-scanner \
                        -Dsonar.projectKey=spring-petclinic-angular \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=YOUR_SONAR_HOST \
                        -Dsonar.login=YOUR_SONAR_TOKEN"
                }
            }
        }
    }
}