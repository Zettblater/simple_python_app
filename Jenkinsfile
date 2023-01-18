pipeline {
    agent any
    environment {
      ANSIBLO_PRIVATE_KEY=credentials('ssh-key')
    }
    triggers { 
      pollSCM '* * * * *'
        }
    stages {
         stage("DEPLOY") {
            steps {
                echo "---Deploy---"
                sh "ansible-playbook:"            }
        }

    }
        stage("TEST") {
            steps {
                echo "---Build---"
                sh "docker build -t zettblater/pipline:v$BUILD_ID ."
            }
        }
        stage("Push DockerHUB") {
            steps {
                echo "---- Push DockerHUB ---- "
                withCredentials([usernamePassword(credentialsId: 's', passwordVariable: 'password', usernameVariable: 'username')]) {
                sh "docker login -u $username -p $password"
                sh "docker push zettblater/pipline:v$BUILD_ID"
}
          
            }
        }
        stage("DEPLOY") {
            steps {
                echo "---Deploy---"
                sh "ansible-playbook playbook.yaml -i inventory --private-key=$ANSIBLE_PRIVATE_KEY -u ansiblo"            }
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
