@Library('github.com/invoca/jenkins-pipeline@master')
def docker = new com.invoca.Docker()

pipeline {
    agent { label 'docker' }
    stages {
        stage('Setup') {
            steps {
                script {
                    imageArgs = [
                        dockerfile: '.',
                        imageName: 'invocaops/base',
                    ]
                }
            }
        }
        stage('Test') {
            steps { sh "./test" }
            post {
                always {
                    junit 'rspec.xml'
                }
            }
        }
        stage('Build') {
            steps { script { docker.imageBuild(imageArgs) } }
        }
        stage('Push') {
            environment {
                DOCKERHUB_USER = credentials('dockerhub_user')
                DOCKERHUB_PASSWORD = credentials('dockerhub_password')
            }
            steps {
                script { docker.imageTagPush(imageArgs.imageName) }
            }
        }
    }

    post {
        always {
            notifySlack(currentBuild.result)
        }
    }
}
