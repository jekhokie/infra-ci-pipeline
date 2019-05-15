pipeline {
    agent any

    environment {
        baseVM     = "CentOS7"
        hardenedVM = "${baseVM}-harden-${env.BUILD_ID}"
    }

    stages {
        stage('Declare Base Image') {
            steps {
                echo 'Base image is...'
                sh 'whoami'
                sh 'pwd'
                sh "/usr/local/bin/vboxmanage showvminfo ${baseVM}"
                echo "Build ID: ${env.BUILD_ID}"
            }
        }
        stage('Security Hardening') {
            steps {
                echo 'Hardening...'
                echo 'Cloning base VM template to new VM...'
                sh "/usr/local/bin/vboxmanage clonevm ${baseVM} --name ${hardenedVM} --register"
            }
        }
        stage('Kernel Tuning') {
            steps {
                sh 'hostname -f'
            }
        }
        stage('Middleware Install') {
            steps {
                echo 'Installing Middleware...'
            }
        }

        stage('Cleanup') {
            steps {
                echo 'Removing VM templates...'
                sh "
            }
        }
    }
}
