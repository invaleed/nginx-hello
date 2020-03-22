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
                    myapp = docker.build("${project}/hello-nginx:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('192.168.65.141/ramadoni', 'harbor') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }        
        stage('Deploy to Kubernetes') {
            steps{
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yaml"
                sh 'kubectl apply -f ./deployment.yml'
            }
        }
    }    
}
