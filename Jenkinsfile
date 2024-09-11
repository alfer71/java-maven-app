#!/usr/bin/env groovy

library identifier: 'jenkins-shared-library@master', retriever: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/alfer71/jenkins-shared-library.git',
    credentialsId: 'gitlab-credentials'
    ]
)

pipeline {
    agent any
    tools {
        maven 'maven-3.9'
    }
    environment {
        IMAGE_NAME = 'alfer/devops-project:java-maven-1.3'
    }
    stages {
        stage('build app') {
            steps {
                echo 'building application jar...'
                buildJar()
            }
        }
        stage("build image") {
            steps {
                script {
                    echo "building the docker image..."
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh '''
                        docker build -t ${IMAGE_NAME} .
                        docker login -u $USER -p $PASS
                        docker push ${IMAGE_NAME}
                        '''
                    }
                }
            }
        }
        stage("deploy") {
            steps {
                script {
                    echo 'deploying docker image to EC2...'
                    def dockerComposeCmd = "docker-compose -f docker-compose.yaml up --detach"
                    sshagent(['aws-ec2-access']) {
                       sh "scp docker-compose.yaml ec2-user@54.160.194.129:/home/ec2-user"
                       sh "ssh -o StrictHostKeyChecking=no ec2-user@54.160.194.129 ${dockerComposeCmd}"
                    }
                }
            }               
        }
    }
}
