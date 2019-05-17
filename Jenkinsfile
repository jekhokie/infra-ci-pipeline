// variable declaration
def hardenedVMIP = ""

pipeline {
    agent any

    environment {
        vbCmd          = "/usr/local/bin/vboxmanage"
        ansibleCmd     = "/usr/local/bin/ansible"
        ansiblePlayCmd = "/usr/local/bin/ansible-playbook"
        baseVM         = "CentOS7"
        hardenedVM     = "${baseVM}-harden-${env.BUILD_ID}"
    }

    stages {
        stage('Job Information') {
            steps {
                echo 'Base image details:'
                sh "${vbCmd} showvminfo ${baseVM}"
                echo "Build ID: ${env.BUILD_ID}"
            }
        }

        stage('Security Hardening') {
            steps {
                echo 'Cloning base VM template to new VM...'
                sh "${vbCmd} clonevm ${baseVM} --name '${hardenedVM}' --register"

                echo 'Starting new VM...'
                sh "${vbCmd} startvm '${hardenedVM}' --type headless"

                echo 'Waiting for boot to get correct IP address...'
                timeout(time: 3, unit: 'MINUTES') {
                    waitUntil {
                        script {
                            def r = sh (
                                script: "${vbCmd} guestproperty wait '${hardenedVM}' '/VirtualBox/GuestInfo/OS/LoggedInUsers'",
                                returnStdout: true
                            )

                            if (r.trim() != 'No value set!') {
                                hardenedVMIP = sh (
                                    script: "${vbCmd} guestproperty get '${hardenedVM}' '/VirtualBox/GuestInfo/Net/0/V4/IP'",
                                    returnStdout: true
                                ).trim().split(': ')[1]

                                return true
                            }
                        }
                    }
                }
                echo "Hardened VM IP Address: ${hardenedVMIP}"

                echo "Wait for IP to be reachable..."
                timeout(time: 3, unit: 'MINUTES') {
                    waitUntil {
                        sh (
                            script: "/sbin/ping -c 1 -t 1 ${hardenedVMIP}",
                            returnStatus: true
                        ) == 0
                    }
                }

                echo "Running smoke tests..."
                sh "${ansibleCmd} --version"
                script {
                    def r = sh (
                        script: "${ansiblePlayCmd} -i ${hardenedVMIP}, -e 'ansible_user=jenkins' ansible/harden_linux_os.yml"
                    )
                }

                echo 'Shutting down hardened VM...'
                sh "${vbCmd} controlvm ${hardenedVM} acpipowerbutton"

                echo 'Waiting for VM to shut down...'
                timeout(time: 5, unit: 'MINUTES') {
                    waitUntil {
                        script {
                            def r = sh (
                                script: "${vbCmd} list runningvms | grep '${hardenedVM}'",
                                returnStatus: true
                            )

                            return (r == 0)
                        }
                    }
                }

                echo 'Removing Hardened VM...'
                sh "${vbCmd} unregistervm '${hardenedVM}' --delete"
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
