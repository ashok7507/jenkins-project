pipeline {
    agent { label 'agent-1' }
    environment {
        APP_LOCATION = "/home/ubuntu/workspace/new/k8s"
        APP_PATH = "/home/ubuntu/workspace/new/"
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

        stage ("deployment") {
            steps {
                dir("${APP_LOCATION}") {
                sh "kubectl apply -f deployment.yaml" 
                }
            }
        }
    }
}
