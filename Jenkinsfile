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
                    myapp = docker.build("${PROJECT_ID}/nginx-hello:${env.BUILD_ID}")
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
            steps{
                sh "sed -i 's/nginx-hello:latest/nginx-hello:${env.BUILD_ID}/g' deployment.yaml"
                sh 'kubectl apply -f ./deployment.yaml'
            }
        }
        stage('Remove Unused docker image') {
            steps{
                sh "docker rmi $registry:$BUILD_NUMBER"
            }
	}
    }    
}
