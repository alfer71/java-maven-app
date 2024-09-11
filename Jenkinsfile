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
        IMAGE_NAME = 'alfer/devops-project:java-maven-1.2'
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
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        echo "Using Docker Hub Username: $USER"
                        sh 'docker build -t ${IMAGE_NAME} .'
                        sh "echo $PASS | docker login -u $USER --password-stdin"
                        sh 'docker push ${IMAGE_NAME}'
                    }
                }
            }
        }
        stage("deploy") {
            steps {
                script {
                    echo 'deploying docker image to EC2...'
                }
            }               
        }
    }
}
