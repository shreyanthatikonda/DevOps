pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
        disableConcurrentBuilds()
    }

    environment {
        DOCKER_IMAGE = 'shreyanthatikonda/webapp:latest'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build --pull -t "$DOCKER_IMAGE" .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKERHUB_USERNAME',
                    passwordVariable: 'DOCKERHUB_TOKEN'
                )]) {
                    sh '''
                        set +x
                        set -eu
                        DOCKER_CONFIG="$(mktemp -d)"
                        export DOCKER_CONFIG
                        trap 'rm -rf -- "$DOCKER_CONFIG"' EXIT
                        printf '%s' "$DOCKERHUB_TOKEN" |
                            docker login --username "$DOCKERHUB_USERNAME" --password-stdin
                        docker push "$DOCKER_IMAGE"
                    '''
                }
            }
        }
    }
}
