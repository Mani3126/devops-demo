pipeline {
    agent any

    environment {
        APP_SERVER = "3.109.55.217"
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t devops-demo .'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker save devops-demo > app.tar
                scp -o StrictHostKeyChecking=no app.tar ubuntu@${APP_SERVER}:~
                ssh -o StrictHostKeyChecking=no ubuntu@${APP_SERVER} "
                    docker stop devops-demo || true &&
                    docker rm devops-demo || true &&
                    docker rmi devops-demo || true &&
                    docker load < app.tar &&
                    docker run -d -p 8080:8080 --name devops-demo devops-demo
                "
                '''
            }
        }
    }
}
