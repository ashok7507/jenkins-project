@Library('shared@main') _

pipeline {
    agent any

    environment {
        APP_PATH   = "/var/lib/jenkins/workspace/shared-pipeline"
        IMAGE_NAME = "vivaa2711/nginx-app"
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
