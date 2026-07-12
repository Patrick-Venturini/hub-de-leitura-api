pipeline {
    agent any

    tools {nodejs "nodejs"}

    stages {
        stage('Clonar a api do Hub de Leitura') {
            steps {
                bat '''
                    if exist hub-de-leitura-api rmdir /s /q hub-de-leitura-api
                    git clone https://github.com/Patrick-Venturini/hub-de-leitura-api.git
                '''
            }
        }

        stage('Instalar dependencias e rodar API no backgroud') {
            steps {
                dir('hub-de-leitura-api') {
                    bat '''
                        call npm install
                        start /B npm start
                    '''
                }
            }
        }

        stage('Clonar projeto de teste') {
            steps {
                    bat '''
                        if exist task-hub-de-leitura-api-cypress-test rmdir /s /q task-hub-de-leitura-api-cypress-test
                        git clone https://github.com/Patrick-Venturini/task-hub-de-leitura-api-cypress-test.git
                    '''
            }
        }

        stage('Instalar dependencias dos testes') {
            steps {
                dir('task-hub-de-leitura-api-cypress-test') {
                    bat 'call npm install'
                }
            }
        }

        stage('Executar os testes da API') {
            steps {
                dir('task-hub-de-leitura-api-cypress-test') {
                    bat 'call npx cypress run'
                }
            }
        }
    }

    post {
        always {
            dir('task-hub-de-leitura-api-cypress-test') {
                archiveArtifacts artifacts: 'cypress/videos/**/*.*,cypress/screenshots/**/*.*', allowEmptyArchive: true
                allure includeProperties: false, jdk: '', results: [[path: 'allure-report']]
            }

            bat 'taskkill /F /IM node.exe || exit 0'
        }
    }
}