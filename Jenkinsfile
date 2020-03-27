pipeline {
    agent any

    environment {
        PROJECT_ID = 'ramadoni'
        APP_NAME = 'nginx-hello'
    }

    stages {
        stage("Checkout code") {
            steps {
                checkout scm
            }
        }

        stage("Build Docker Image") {
            steps {
                script {
                    if (env.BRANCH_NAME == 'dev') {
                        app = docker.build("${PROJECT_ID}/${APP_NAME}-dev")
                    } else {
                        app = docker.build("${PROJECT_ID}/${APP_NAME}-prod")
                    }
                }
            }
        }
    }    
}
