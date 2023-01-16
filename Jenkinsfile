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
}
