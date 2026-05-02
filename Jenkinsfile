pipeline {
    agent any

    environment {
        IMAGE_NAME = "satyam4853/nginx-demo"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/your-username/nginx-demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh 'docker login -u $USER -p $PASS'
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $IMAGE_NAME'
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                docker stop nginx-demo || true
                docker rm nginx-demo || true
                docker run -d -p 8081:80 --name nginx-demo $IMAGE_NAME
                '''
            }
        }
    }
}
