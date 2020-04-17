pipeline {
    agent any

    environment {
        IMAGE_TO_ATTEST='gcr.io/liveramp-eng-ops-dev/echoserver@sha256:10f4dbc8eeeb8806d9b3a261b2473b77ca357b290a15d91ce5a0ca5e6164b535'
        ATTESTATION_PROJECT_ID='liveramp-eng-ops-dev'
        ATTESTOR='jenkins-dev-attestor'
        PUBLIC_KEY_ID='ni:///sha-256;hPVdYFSICvLyPaWKDJkRl5pRTEM2hBhMe1OagyzdMe0'
    }

    stages {
        stage('Create Image Attestation') {
            steps {
                sh '''
                  // This shouldn't be needed. Probably because I updated
                  // statefulset to become root and install gcloud,kubectl etc
                  su - jenkins

                  // Create attestation payload
                  gcloud container binauthz create-signature-payload --artifact-url="${IMAGE_TO_ATTEST}" > /tmp/generated_payload.json

                  // Create signature (binary file) using private key file
                  openssl dgst -sha256 -sign /tmp/ec_private.pem /tmp/generated_payload.json > /tmp/ec_signature

                 // Create Attestation
                 gcloud container binauthz attestations create --project="${ATTESTATION_PROJECT_ID}" --artifact-url="${IMAGE_TO_ATTEST}" --attestor="projects/${ATTESTOR_PROJECT_ID}/attestors/${ATTESTOR}" --signature-file=/tmp/ec_signature --public-key-id="${PUBLIC_KEY_ID}"
                '''
            }
        }

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
    }
}
