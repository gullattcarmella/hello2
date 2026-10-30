pipeline {
    agent any

    environment {
        DOCKERHUB_REPO = 'gullattcarmella/hello'
        IMAGE_TAG = new Date().format('yyyyMMdd-HHmm')
    }

    stages {
        stage('Git Clone') {
            steps {
                git branch: 'main',
                    url: 'git@github.com:gullattcarmella/hello2.git',
                    credentialsId: 'github-ssh'
            }
        }

        stage('Build Docker image') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'docker version'
                        sh "docker build -t ${env.DOCKERHUB_REPO}:${env.IMAGE_TAG} ."
                    } else {
                        bat 'docker version'
                        bat "docker build -t ${env.DOCKERHUB_REPO}:${env.IMAGE_TAG} ."
                    }
                }
            }
        }

        stage('Push Docker image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_TOKEN'
                )]) {
                    script {
                        if (isUnix()) {
                            sh 'echo "$DOCKER_TOKEN" | docker login -u "$DOCKER_USER" --password-stdin'
                            sh 'docker push "$DOCKERHUB_REPO:$IMAGE_TAG"'
                        } else {
                            bat '@echo off\nset "DOCKER_CONFIG=%WORKSPACE%\\.docker"\nif not exist "%DOCKER_CONFIG%" mkdir "%DOCKER_CONFIG%"\necho %DOCKER_TOKEN%| docker login -u "%DOCKER_USER%" --password-stdin'
                            bat 'docker push %DOCKERHUB_REPO%:%IMAGE_TAG%'
                        }
                    }
                }
            }
        }
    }
}
