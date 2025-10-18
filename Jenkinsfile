pipeline {
    agent any

    environment {
        IMAGE_NAME = "varunv25/myimg1:latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/varunkumarv1997/pro2/'
                echo '✅ Code checked out from GitHub'
            }
        }

        stage('Compile Code') {
            steps {
                echo '⚙️ Compiling code...'
                sh 'mvn clean compile'
            }
        }

        stage('Run Tests') {
            steps {
                echo '🧪 Running tests...'
                sh 'mvn test'
            }
        }

        stage('Code Quality Check') {
            steps {
                echo '🔍 Checking code quality...'
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('Package Application') {
            steps {
                echo '📦 Packaging application...'
                sh 'mvn package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🐳 Building Docker image...'
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo '📤 Pushing image to Docker Hub...'
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
                echo '🚀 Deploying application to Kubernetes...'
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_FILE')]) {
                    script {
                        sh '''
                            export KUBECONFIG=$KUBECONFIG_FILE
                            kubectl set image deployment/mydeployment healthcare-container=${IMAGE_NAME} --record || \
                            kubectl apply -f deployment.yml
                            kubectl rollout status deployment/mydeployment
                            kubectl get svc
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully! App deployed on Kubernetes."
        }
        failure {
            echo "❌ Pipeline failed. Check Jenkins logs for details."
        }
    }
}
