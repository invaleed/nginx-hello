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
                        echo 'I only execute on the master branch' 
                    } else {
                        echo 'I execute elsewhere'
                    }
                }
            }
        }
    }    
}
