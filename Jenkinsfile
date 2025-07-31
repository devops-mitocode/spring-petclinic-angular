pipeline {
    agent any
    parameters {
        choice(name: 'ENVIRONMENT', choices: ['ephemeral', 'dev'], description: 'Selecciona el entorno')
        choice(name: 'BROSWER', choices: ['chrome', 'edge', 'firefox'], description: 'Selecciona el navegador')
        string(name: 'TAGS', defaultValue: '@listarPropietarios', description: 'Especifica los tags')
    }
    stages {
        stage('Prepare environment') {
            steps {



                dir('e2e-tests'){
                    git branch: "master",
                    url: "https://github.com/devops-mitocode/e2e-tests-12-24.git"
                }
                sh 'cd e2e-tests && ls -la'
                sh 'env | sort'
                sh 'docker system df'
                sh "docker compose --project-name ${BUILD_TAG} up -d --quiet-pull"
                sh 'docker ps -a'
                sh 'docker network ls'
            }
        }
        stage('End2End Tests') {
            steps {
                script {
                    sh 'docker ps -a'
                    // def tagsOption = TAGS?.trim() ? "-Dcucumber.filter.tags='${TAGS}'" : ""
                    // sh "docker exec ${BUILD_TAG} mvn clean verify -Denvironment=${ENVIRONMENT} -Dwebdriver.remote.url=http://${BUILD_TAG}-selenium-hub-1:4444/wd/hub -Dwebdriver.remote.driver=${BROSWER} ${tagsOption} -B -ntp"
                }
//                publishHTML(
//                    target: [
//                        reportName           : 'Serenity Report',
//                        reportDir            : 'target/site/serenity',
//                        reportFiles          : 'index.html',
//                        keepAll              : true,
//                        alwaysLinkToLastBuild: true,
//                        allowMissing         : false
//                    ]
//                )
            }
        }
    }
    post {
        always {
            sh "docker compose --project-name ${BUILD_TAG} down --volumes"
            sh 'docker system df'
            cleanWs()
        }
    }
}