pipeline {
    agent any
    tools {
        MAVEN 'mvn'
    }
    environment {
        IMAGE_NAME = '1.1'
        DOCKER_REPO = 'dawit91/my-repo'
    }
    stages {
        stage('build package') {
            steps {
                script {
                    sh 'mvn clean package'
                }
            }
        }
        stage('build image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh "docker build -t ${DOCKER_REPO}:${IMAGE_NAME}"
                        sh "echo $PASSWORD | docker login -u $USER --pass-stdin"
                        sh "docker push ${DOCKER_REPO}:${IMAGE_NAME}"
                    }
                }
            }
        }
        stage('Deploy') {
            environment {
                JAVA_APP = "${DOCKER_REPO}:${IMAGE_NAME}"
            }
            steps {
                script {
                    withKubeConfig([credentialsId: 'kubernetes', serverUrl: 'https://api.k8s.my-company.com']) {
                        withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                            sh "kubectl create secret docker-registry my-registry-key --docker-server=docker.io --docker-username=$USERNAME --docker-password=$PASSWORD"
                            sh "envsubst kuberentes/deployment.yaml | kubectl create -f -"
                        }
                    }
                }
            }
        }

    }

}