pipeline {
    agent any
    triggers { 
      polSCM '* * * * *'
        }
    stages {
        
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
                sh "docker run -d --name flask-app-$BUILD_ID -p 80$BUILD_ID:8080 zettblater/pipline:v$BUILD_ID"
            }
        }
    }
}
