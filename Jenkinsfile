node {
    def app
    def project = 'ramadoni'
    def appName = 'nginx-hello'

    stage('Clone repository') {
      checkout scm
    }

    stage('Build Docker Image') {
      echo env.BRANCH_NAME
      if (env.BRANCH_NAME == 'dev') {
        echo "this is dev branch"
      } else {
        echo "this is master branch"
      }
    }
}
