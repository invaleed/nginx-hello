pipeline {
    agent any
    environment {
        project_id = 'ramadoni'
        app_name = 'nginx-hello'
    }

    stages {

        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'origin/master') {
                        myapp = docker.build("${project_id}/${app_name}-prod:${env.BUILD_NUMBER}")
                    } else {
                        myapp = docker.build("${project_id}/${app_name}-dev:${env.BUILD_NUMBER}")
                    }
                }
            }
        }

        stage('Push Images') {
            steps {
                script {
                    docker.withRegistry('https://docker.adzkia.web.id/ramadoni', 'harbor') {
                        myapp.push("${env.BUILD_NUMBER}")
                        myapp.push("latest")
                    }
                }
            }
        }

        stage('Deploy to K8s') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'origin/master') {
                        sh "sed -i 's/${app_name}:latest/${app_name}-prod:${env.BUILD_NUMBER}/g' deployment.yaml"
                        sh 'kubectl apply -f ./deployment.yaml -n prod'
                    } else {
                        sh "sed -i 's/${app_name}:latest/${app_name}-dev:${env.BUILD_NUMBER}/g' deployment.yaml"
                        sh 'kubectl apply -f ./deployment.yaml -n dev'
                    }
                }
            }
        }
    }
}
