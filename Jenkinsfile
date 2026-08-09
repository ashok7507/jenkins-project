pipeline {
    agent {label "agent-1"}
    environment {
        APP_PATH = "/home/ubuntu/workspace/git+dockerbuild-pipeline"
        IMAGE_NAME = "ashok7507/nginx-app"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Clone') {
            steps {
                echo "cloning project from github to jenkins-server"
                git branch: 'main',
                credentialsId: 'github-cred',
                    url: 'https://github.com/ashok7507/demo.git'
                    echo "sucessfully cloning repo"
            }
        }

        stage('Build Docker Image') {
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
    }
}
