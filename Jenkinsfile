#!groovy

pipeline {
  options {
    ansiColor('xterm')
    disableConcurrentBuilds()
    // This discards builds after there are either 50 unique builds
    // or a build is older than 30 days
    buildDiscarder(logRotator(numToKeepStr: '50', daysToKeepStr: '30'))
  }

  triggers {
    // Github hook to build whenever a commit is pushed
    githubPush()
    // This creates a trigger to run dependency builds
    snapshotDependencies()
    // Github hook to build whenever 'jenkins please' is in a comment on github
    issueCommentTrigger('.*jenkins\\W+(((test|build|run|do)\\W+this)|again|git\\W+r\\W+done|please|make it so).*')
  }

  agent any
  environment {
      OPS_DEV_PROJECT = 'liveramp-eng-ops-dev'
      OPS_DEV_CLUSTER = 'ops-dev'
  }

  stages {
    stage('build: dry run on ops-dev') {
      when { { branch 'master' } }
      steps {
          withCredentials( bindings:
              [
              file(
                  credentialsId: 'liveramp-eng-ops-dev',
                  variable: 'GOOGLE_APPLICATION_CREDENTIALS'
              )
              ]) {
          sh './deploy ${OPS_DEV_CLUSTER} ${OPS_DEV_PROJECT} stable --dry-run=true'
        }
      }
    }

    stage('dry-run deploy:ops-dev') {
      when { branch 'master'}
      steps {
          withCredentials( bindings:
              [
              file(
                  credentialsId: 'liveramp-eng-ops-dev',
                  variable: 'GOOGLE_APPLICATION_CREDENTIALS'
              )
              ]) {
          sh './deploy ${OPS_DEV_CLUSTER} ${OPS_DEV_PROJECT} stable --dry-run=true'
        }
      }
    }
  }
}
