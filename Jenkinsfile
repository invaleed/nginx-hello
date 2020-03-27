node {
    def app
    def project = 'ramadoni'
    def appName = 'nginx-hello'

    stage('Clone repository') {
      checkout scm
    }

    stage('Build Docker Image') {
      if (env.BRANCH_NAME == 'origin/dev') {
        app = docker.build("${project}/${appName}-dev")
      } else {
        app = docker.build("${project}/${appName}-prod")
      }
    }

    stage('Test Image - Bisa diabaikan') {
        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push Image to Registry') {
        docker.withRegistry('https://192.168.65.141/ramadoni', 'harbor') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
      }
    }

    stage('Deploy to Kubernetes') {
      if (env.BRANCH_NAME == 'origin/dev') {
          sh "sed -i 's/${appName}:latest/${appName}-dev:${env.BUILD_NUMBER}/g' deployment.yaml"
          sh 'kubectl apply -f ./deployment.yml -n dev'
      } else {
          sh "sed -i 's/${appName}:latest/${appName}-prod:${env.BUILD_NUMBER}/g' deployment.yaml"
          sh 'kubectl apply -f ./deployment.yml -n prod'
      }
    }

    stage('Remove local images') {
        // remove local docker images
          // sh 'docker system prune -a -f'
    }
}

