def branch = "master"
def remoteurl = "https://github.com/malikalrizky/dumbflix-frontend.git"
def remotename = "jenkins-fe"
def workdir = "~/dumbflix-frontend/"
def ip = "116.193.191.120"
def username = "kelompok1"
def imagename = "dumbways-fe"
def sshkeyid = "app-server"
def telegramapi = "5671860635:AAFmz1Wp2zX_XS_Ur3c5OXjyKCG2k9bxLL8"
def telegramid = "-707481763"
def dockerusername = "malikalrk"

pipeline {
    agent any

    stages {
        stage('Pull From Frontend Repo') {
            steps {
                sshagent(credentials: ["${sshkeyid}"]) {
                    sh """
                        ssh -l ${username} ${ip} <<pwd
                        cd ${workdir}
                        git remote add ${remotename} ${remoteurl} || git remote set-url ${remotename} ${remoteurl}
                        git pull ${remotename} ${branch}
                        pwd
                        hostname
                        pwd
                    """
                }
            }
        }
            
        stage('Build Docker Image') {
            steps {
                sshagent(credentials: ["${sshkeyid}"]) {
                    sh """
                        ssh -l ${username} ${ip} <<pwd
                        cd ${workdir}
                        docker build -t ${imagename}:${env.BUILD_ID} .
                        pwd
                    """
                }
            }
        }
            
        stage('Deploy Image') {
            steps {
                sshagent(credentials: ["${sshkeyid}"]) {
                    sh """
                        ssh -l ${username} ${ip} << pwd
                        cd ${workdir}
                        docker compose down
                        docker tag ${imagename}:${env.BUILD_ID} ${imagename}:latest
                        docker compose up -d
                        pwd
                    """
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sshagent(credentials: ["${sshkeyid}"]) {
                        sh """
                                ssh -l ${username} ${ip} <<pwd
                                docker tag ${imagename}:${env.BUILD_ID} ${dockerusername}/${imagename}:${env.BUILD_ID}
                                docker tag ${imagename}:latest ${dockerusername}/${imagename}:latest
                                docker image push ${dockerusername}/${imagename}:latest
                                docker image push ${dockerusername}/${imagename}:${env.BUILD_ID}
                                docker image rm ${dockerusername}/${imagename}:latest
                                docker image rm ${dockerusername}/${imagename}:${env.BUILD_ID}
                                docker image rm ${imagename}:${env.BUILD_ID}
                                pwd
                        """
                }
            }
        }

        stage('Send Success Notification') {
            steps {
                sh """
                    curl -X POST 'https://api.telegram.org/bot${telegramapi}/sendMessage' -d \
		    'chat_id=${telegramid}&text=Build ID #${env.BUILD_ID} Frontend Pipeline Successful!'
                """
            }
        }
 }
}
