pipeline {
    agent any
    options {
       timeout(time: 10, unit: 'MINUTES')
       ansiColor('xterm')
    }    
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
                sh '''
                    docker --version && docker compose version
                    docker system df
                    docker compose --project-name ${BUILD_TAG} pull --quiet
                    docker compose --project-name ${BUILD_TAG} up -d
                    docker compose --project-name ${BUILD_TAG} ps

                    docker ps -a
                    docker ps -q | xargs -I {} docker inspect --format 'Nombre: {{.Name}} Hostname: {{.Config.Hostname}} IP: {{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' {}
                    docker network ls
                '''                
            }
        }
        stage('End2End Tests') {
            steps {
                script {
                    def tagsOption = TAGS?.trim() ? "-Dcucumber.filter.tags='${TAGS}'" : ""
                    sh '''
                        docker compose --project-name ${BUILD_TAG} exec -T maven mvn clean verify \
                        -Denvironment=${ENVIRONMENT} \
                        -Dwebdriver.remote.url=http://${BUILD_TAG}-selenium-hub-1:4444/wd/hub \
                        -Dwebdriver.remote.driver=${BROSWER} \
                        -Dstyle.color=always \
                        ${tagsOption} -B -ntp
                    '''
                }
                publishHTML(
                    target: [
                        reportName           : 'Serenity Report',
                        reportDir            : 'e2e-tests/target/site/serenity',
                        reportFiles          : 'index.html',
                        keepAll              : true,
                        alwaysLinkToLastBuild: true,
                        allowMissing         : false
                    ]
                )
            }
        }
    }
    post {
        always {
            sh '''
                docker compose --project-name ${BUILD_TAG} down --rmi all --volumes --remove-orphans
                docker builder prune -af
                docker system df
            '''
            cleanWs()
        }
    }
}