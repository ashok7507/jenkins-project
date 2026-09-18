pipeline {
    agent any
    environment {
        APP_PATH = "/var/lib/jenkins/workspace/new-shared"
        IMAGE_NAME = "ashok7507/xyz-image"
        IMAGE_TAG = "latest"
    }
    stages {
        stage('checkout') {
            steps {
                git branch: "main",
                credentialsId: "github-cred",
                url: "https://github.com/ashok7507/jenkins-project.git"
            }
        }
        stage('build') {
            steps {  
               dir("${APP_PATH}") {
                   sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
               }
            }
        }
        stage('Docker Hub Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-cred',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]){
                sh " docker login -u ${env.DOCKER_USERNAME} -p ${env.DOCKER_PASSWORD} "
               }
            }
        }
        stage('Push Docker Image') {
            steps {
                sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh """
                    kubectl apply -f k8s/
                """
            }
        }

        stage('Check Kubernetes Deployment') {
            steps {
                sh """
                    kubectl get pods
                    kubectl get deployments
                    kubectl get services
                """
            }
        }
    }
}
