pipeline {
    agent any
    triggers {
      pollSCM '* * * * *'
	}
    stages {
        stage("TEST") {
            steps {
                echo "--- Tests ---"
            }
        }
        stage("Build") {
            steps {
                echo "--- Build ---"
                sh "docker build -t rufatzakirov/first_pipeline:v$BUILD_ID ."
            }
        }
        stage("Push DockerHUB") {
            steps {
                echo "--- Push DockerHUB ---"
                withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'pass', usernameVariable: 'user')]) {
                sh "docker login -u $user -p $pass"
                sh "docker push rufatzakirov/first_pipeline:v$BUILD_ID"
                }
            }
        }
        stage("Deploy") {
            steps {
                echo "--- Deploy ---"
                sh "docker run -d --name flask-app-$BUILD_ID -p 80$BUILD_ID:8080 rufatzakirov/first_pipeline:v$BUILD_ID"
            }
        }
    }
    post {
     success { 
        withCredentials([string(credentialsId: 'botSecret', variable: 'TOKEN'), string(credentialsId: 'chatId', variable: 'CHAT_ID')]) {
        sh  ("""
            curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='*${env.JOB_NAME}* : POC *Branch*: ${env.GIT_BRANCH} *Build* : OK *Published* = YES'
        """)
        }
     }

     aborted {
        withCredentials([string(credentialsId: 'botSecret', variable: 'TOKEN'), string(credentialsId: 'chatId', variable: 'CHAT_ID')]) {
        sh  ("""
            curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='*${env.JOB_NAME}* : POC *Branch*: ${env.GIT_BRANCH} *Build* : `Aborted` *Published* = `Aborted`'
        """)
        }
     
     }
     failure {
        withCredentials([string(credentialsId: 'botSecret', variable: 'TOKEN'), string(credentialsId: 'chatId', variable: 'CHAT_ID')]) {
        sh  ("""
            curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='*${env.JOB_NAME}* : POC  *Branch*: ${env.GIT_BRANCH} *Build* : `not OK` *Published* = `no`'
        """)
        }
     }

 }

}
