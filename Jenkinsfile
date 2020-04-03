#!groovy

pipeline {
  options {
    disableConcurrentBuilds()
    // This discards builds after there are either 50 unique builds
    // or a build is older than 30 days
    buildDiscarder(logRotator(numToKeepStr: '50', daysToKeepStr: '30'))
  }

  triggers {
    // Github hook to build whenever a commit is pushed
    githubPush()
  }

  agent any
  environment {
      OPS_DEV_PROJECT = 'liveramp-eng-ops-dev'
      OPS_DEV_CLUSTER = 'ops-dev'
  }

  stages {
    stage('build: dry run on ops-dev') {
      when { branch 'master' }
      steps {
          withCredentials( bindings:
              [
              file(
                  credentialsId: 'liveramp-eng-ops-dev',
                  variable: 'GOOGLE_APPLICATION_CREDENTIALS'
              )
              ]) {
          sh './deploy ${OPS_DEV_CLUSTER} ${OPS_DEV_PROJECT} stable --dry-run=false'
        }
      }
    }
  }
}
