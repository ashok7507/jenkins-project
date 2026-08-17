@Library('Jenkins-shared-lib') _

pipeline {
    agent {
        label 'agent-1'
    }

    environment {
        APP_PATH   = "/home/ubuntu/workspace/git-pipeline"
        IMAGE_NAME = "ashok7507/nginx-app"
        IMAGE_TAG  = "latest"
    }

    stages {

        stage('clone') {
            steps {
                clone(
                    'main',
                    'https://github.com/ashok7507/demo.git',
                    'github-cred'
                )
            }
        }

        stage('buildDockerImage') {
            steps {
                buildDockerImage(
                    env.APP_PATH,
                    env.IMAGE_NAME,
                    env.IMAGE_TAG
                )
            }
        }

        stage('dockerLogin') {
            steps {
                dockerLogin('dockerhub-cred')
            }
        }

        stage('pushDockerImage') {
            steps {
                pushDockerImage(
                    env.IMAGE_NAME,
                    env.IMAGE_TAG
                )
            }
        }
    }
}
