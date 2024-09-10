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
                echo 'Building application JAR...'
                buildJar()
            }
        }
        stage('build image') {
            steps {
                script {
                    echo 'Building the Docker image...'
                    buildImage(env.IMAGE_NAME)
                    dockerLogin()
                    dockerPush(env.IMAGE_NAME)
                }
            }
        }
        stage('deploy') {
            steps {
                script {
                    echo 'Deploying Docker image to EC2...'
                    
                    // Set the Docker run command
                    def dockerCmd = "docker run -p 8080:8080 -d ${env.IMAGE_NAME}"
                    
                    sshagent(['aws-ec2-access']) {
                        // Avoid interpolation issues by using a multiline script without interpolation
                        sh '''
                        ssh -o StrictHostKeyChecking=no ec2-user@3.89.247.112 <<EOF
                        docker run -p 8080:8080 -d ''' + "${env.IMAGE_NAME}" + '''
                        EOF
                        '''
                    }
                }
            }
        }
    }
}
