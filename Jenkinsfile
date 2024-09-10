#!/usr/bin.env groovy

pipeline {   
    agent any
    stages {
        stage("test") {
            steps {
                script {
                    echo "Testing the application..."

                }
            }
        }
        stage("build") {
            steps {
                script {
                    echo "Building the application..."
                }
            }
        }

        stage("deploy") {
            steps {
                script {
                    def dockerCmd = 'docker run -p 3080:3080 -d alfer/devops-project:jma-1.1'
                    sshagent(['aws-ec2-access']) {
                       sh "ssh -o StrictHostKeyChecking=no ec2-user@54.160.194.129 ${dockerCmd}"      
                    }
                }
            }
        }               
    }
} 

