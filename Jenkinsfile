pipeline {
    agent any

    environment {
        vbCommand  = "/usr/local/bin/vboxmanage"
        baseVM     = "CentOS7"
        hardenedVM = "${baseVM}-harden-${env.BUILD_ID}"
    }

    stages {
        stage('Declare Base Image') {
            steps {
                echo 'Base image is...'
                sh 'whoami'
                sh 'pwd'
                sh "${vbCommand} showvminfo ${baseVM}"
                echo "Build ID: ${env.BUILD_ID}"
            }
        }
        stage('Security Hardening') {
            steps {
                echo 'Hardening...'

                echo 'Cloning base VM template to new VM...'
                sh "${vbCommand} clonevm ${baseVM} --name '${hardenedVM}' --register"

                echo 'Starting new VM...'
                sh "${vbCommand} startvm '${hardenedVM}' --type headless"

                echo 'Waiting for IP address...'
                timeout(time: 3, unit: 'MINUTES') {
                    waitUntil {
                        script {
                            def r = sh (
                                script: "${vbCommand} guestproperty get '${hardenedVM}' '/VirtualBox/GuestInfo/Net/0/V4/IP'",
                                returnStdout: true
                            )
                            println("$r")
                            return (r.trim() != 'No value set!')
                        }
                    }
                }
                echo "${r.trim()}"

                echo 'Removing Hardened VM...'
                sh "${vbCommand} unregistervm '${hardenedVM}' --delete"
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
            }
        }
    }
}
