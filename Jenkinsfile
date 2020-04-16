pipeline {
    agent any
    stages {
        stage('Deploy Staging') {
            steps{
                git url: 'https://github.com/Test-asarang/echoheaders'
                step([$class: 'KubernetesEngineBuilder', 
                        projectId: "liveramp-eng-ops-dev",
                        clusterName: "ops-dev",
                        location: "us-central1",
                        manifestPattern: 'kubernetes/',
                        namespace: "staging-deploy",
                        credentialsId: "liveramp-eng-ops-dev"])
            }
        }
        stage('Wait for Approval') {
         steps{
           timeout(time:12, unit:'HOURS') {
              input message:'Approve deployment?', submitter: 'approver'
           }
         }
        }
        stage('Deploy Prod') {
            steps{
                git url: 'https://github.com/Test-asarang/echoheaders'
                step([$class: 'KubernetesEngineBuilder', 
                        projectId: "liveramp-eng-ops-dev",
                        clusterName: "ops-dev",
                        location: "us-central1",
                        manifestPattern: 'kubernetes/',
                        namespace: "prod-deploy",
                        credentialsId: "liveramp-eng-ops-dev"])
            }
        }
    }
}
