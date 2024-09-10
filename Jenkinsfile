#!/usr/bin/env groovy

library identifier: 'jenkins-shared-library@master', retriever: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/alfer71/jenkins-shared-library.git',
    credentialsID: 'gitlab-credentials'
    ]
)

pipeline {
    agent any
    tools {
        maven 'maven-3.9'
    }
    environment {
        IMAGE_NAME = 'alfer/devops-project:java-maven-1.0'
    }
    stages {
        stage('build app') {
            steps {
                echo 'building application jar...'
                buildJar()
            }
        }
        stage('build image') {
            steps {
                script {
                    echo 'building the docker image...'
                    buildImage(env.IMAGE_NAME)
                    dockerLogin()
                    dockerPush(env.IMAGE_NAME)
                }
            }
        }
        stage('deploy') {
            steps {
                script {
                    echo 'deploying docker image to EC2...'
                    def dockerCmd = "docker run -p 8080:8080 -d ${IMAGE_NAME}"
                    sshagent(['aws-ec2-access']) {
                        withCredentials([sshUserPrivateKey(credentialsId: 'aws-ec2-access', keyFileVariable: 'EC2_KEY')]) {
                            // Pass dockerCmd as an environment variable
                            withEnv(["DOCKER_CMD=${dockerCmd}"]) {
                                sh '''
                                    ssh -o StrictHostKeyChecking=no -i $EC2_KEY ec2-user@54.160.194.129 "$DOCKER_CMD"
                                '''
                            }
                        }
                    }
                }
            }
        }
    }
}
