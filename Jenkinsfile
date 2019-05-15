pipeline {
    agent any

    stages {
        stage('Declare Base Image') {
            steps {
                echo 'Base image is...'
                sh 'whoami'
                sh 'pwd'
                sh '/usr/local/bin/vboxmanage showvminfo CentOS7'
            }
        }
        stage('Security Hardening') {
            steps {
                echo 'Hardening...'
                echo 'Cloning base VM template to new VM...'
                sh ''
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
    }
}
