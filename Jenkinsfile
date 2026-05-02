pipeline {
    agent any

    environment {
        IMAGE_NAME = "satyam4853/nginx-demo"
        TAG = "${BUILD_NUMBER}"
        RELEASE = "nginx-release"
    }


    stages {

        stage('Checkout') {
            steps {
                git  branch: 'Development' , url: 'https://github.com/satyam4853/nginx.git'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('sonar-token') {
                    sh 'sonar-scanner'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$TAG .'
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
                sh 'docker push $IMAGE_NAME:$TAG'
            }
        }

        stage('Deploy via Helm') {
            steps {
                sh '''
                helm upgrade --install $RELEASE ./nginx-chart \
                  --set image.repository=$IMAGE_NAME \
                  --set image.tag=$TAG
                '''
            }
        }
    }
}
