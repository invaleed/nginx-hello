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
                    if (env.BRANCH_NAME == 'origin/dev') {
                        myapp = docker.build("${PROJECT_ID}/nginx-hello-dev:${env.BUILD_ID}")
                        } else {
                        myapp = docker.build("${PROJECT_ID}/nginx-hello-prod:${env.BUILD_ID}")
                    }
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://192.168.65.141/ramadoni', 'harbor') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'origin/dev') {
                        sh "sed -i 's/nginx-hello:latest/nginx-hello-dev:${env.BUILD_ID}/g' deployment.yaml"
                        sh 'kubectl apply -f ./deployment.yaml -n dev'
                    } else {
                        sh "sed -i 's/nginx-hello:latest/nginx-hello-prod:${env.BUILD_ID}/g' deployment.yaml"
                        sh 'kubectl apply -f ./deployment.yaml -n prod'
                    }
                }                   
            }
        }
        stage('Remove Unused docker image') {
            steps {
                // sh "docker rmi ${PROJECT_ID}/nginx-hello:${env.BUILD_ID}"
                // sh "docker rmi 192.168.65.141/${PROJECT_ID}/nginx-hello:${env.BUILD_ID}"
            }
        }
    }
}
