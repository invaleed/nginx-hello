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

        stage("Build image") {
            steps {
                script {
                    if (env.BRANCH_NAME == 'dev') {
                        app = docker.build("${project}/${appName}-dev")
                    } else {
                        app = docker.build("${project}/${appName}-prod")
                    }
                }
            }
        }
    }    
}
