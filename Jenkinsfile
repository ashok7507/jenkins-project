pipeline {
    agent { label "agent-1" }

    environment { 
        APP_PATH = "/home/ubuntu/workspace/jenkins-project"
        IMAGE_NAME = "ashok7507/nginx-app"
        IMAGE_TAG  = "latest"
        K8S_CONTEXT = "kind-tws-cluster"
        K8S_DEPLOYMENT = "nginx-deployment"
        K8S_CONTAINER = "nginx"
    }

    stages {

        stage('Clone') {
            steps {
                echo "cloning project from github to jenkins-server"
                git branch: 'main',
                credentialsId: 'github-cred',
                    url: 'https://github.com/ashok7507/jenkins-project.git'
                    echo "sucessfully cloning repo"
            }
        }

        stage('Check Kubernetes') {
            steps {
                sh '''
                    echo "Checking Kubernetes cluster..."

                    kubectl config use-context ${K8S_CONTEXT}

                    kubectl cluster-info

                    kubectl get nodes

                    echo "Kubernetes cluster is accessible"
                '''
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
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-cred',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                            --username "$DOCKER_USERNAME" \
                            --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "Pushing image to Docker Hub..."

                sh '''
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "Deploying application to Kubernetes..."

                sh '''
                    kubectl config use-context ${K8S_CONTEXT}

                    kubectl apply -f deployment.yaml
                    kubectl apply -f service.yaml

                    kubectl set image deployment/${K8S_DEPLOYMENT} \
                        ${K8S_CONTAINER}=${IMAGE_NAME}:${IMAGE_TAG}

                    kubectl rollout status deployment/${K8S_DEPLOYMENT} \
                        --timeout=120s
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                echo "Checking Kubernetes deployment..."

                sh '''
                    kubectl get deployment
                    kubectl get pods -o wide
                    kubectl get service
                '''
            }
        }
    }

    post {
        success {
            echo "CI/CD pipeline completed successfully!"
            echo "Docker image: ${IMAGE_NAME}:${IMAGE_TAG}"
        }

        failure {
            echo "Pipeline failed. Check the stage logs above."
        }

        always {
            echo "Build completed: ${BUILD_NUMBER}"
        }
    }
}
