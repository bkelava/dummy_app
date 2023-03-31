pipeline {
    agent {
        label 'agent1'
    }
    stages {
        stage ('Clean up') {
            steps {
                deleteDir()
            }
        }
        stage('Checkout') {
            steps {
                sh 'git clone https://github.com/bkelava/dummy_app.git'
            }
        }
        stage('Tests') {
            steps {
                sh 'cat /etc/os-release'
            }
        }
        stage('Build images') {
            steps {
                dir('dummy_app/auth-api') {
                    sh """
                        docker build -t kelava/kub-demo-auth:${env.BUILD_NUMBER} .
                    """
                }
                dir('dummy_app/frontend') {
                    sh """#!/bin/bash
                        docker build -t kelava/kub-demo-frontend:${env.BUILD_NUMBER} .
                    """
                }
                dir('dummy_app/tasks-api') {
                    sh """#!/bin/bash
                        docker build -t kelava/kub-demo-tasks:${env.BUILD_NUMBER} .
                    """
                }
                dir('dummy_app/users-api') {
                    sh """#!/bin/bash
                        docker build -t kelava/kub-demo-users:${env.BUILD_NUMBER} .
                    """
                }
            }
        }
        stage("Push images") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerhubPassword', usernameVariable: 'dockerhubUser')]) {
                    sh """#!/bin/bash
                        docker login -u ${env.dockerhubUser} -p ${env.dockerhubPassword}
                    """
                    sh """#!/bin/bash
                        docker push kelava/kub-demo-auth:${env.BUILD_NUMBER}
                    """
                    sh """#!/bin/bash
                        docker push kelava/kub-demo-frontend:${env.BUILD_NUMBER}
                    """
                    sh """#!/bin/bash
                        docker push kelava/kub-demo-tasks:${env.BUILD_NUMBER}
                    """
                    sh """#!/bin/bash
                        docker push kelava/kub-demo-users:${env.BUILD_NUMBER}
                    """
                }
            }
        }
        stage("Trigger Kubernetes pipeline") {
            steps {
                build job: 'Kubernetes Deployment', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
            }
        }
    }
}
