pipeline {
    agent any

    stages {
        stage('Declare Base Image') {
            steps {
                echo 'Base image is...'
                sh 'vboxmanage showvminfo CentOS7'
            }
        }
        stage('Security Hardening') {
            steps {
                echo 'Hardening...'
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
