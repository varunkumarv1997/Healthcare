pipeline {
    agent any
    environment {
        IMAGE_NAME = "varunv25/myimg1:latest"
    }
    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/varunkumarv1997/pro2/'
                echo 'GitHub code checked out'
            }
        }
        stage('Compile Code') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Test Code') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Check Quality') {
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }
        stage('Package App') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh "docker build -t $IMAGE_NAME ."
            }
        }
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $IMAGE_NAME
                    '''
                }
            }
        }
        stage('Deploy to Kubernetes') {
    steps {
        withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
            sh 'kubectl apply -f deployment.yml'
        }
    }
}
    }
}
