pipeline {
    environment {
        registry = 'hechichu/python-application-docker-image-sample'
        registryCredentials = 'DOCKERHUB-ACCESS-TOKEN'
    }
    agent any
    stages {
        stage('Cloning our github repository') {
            steps {
                git credentialsId: 'GIT-ACCESS-TOKEN', url: 'https://github.com/hechichu/app-py.git', branch: 'main'
            }
        }
        stage('Build docker image') {
            steps {
                sh 'cd Henry && docker build -t app-py-image . '
            }
        }
        stage('Tagging docker image') {
            steps {
                sh 'docker tag app-py-image $registry:latest'
                sh 'docker tag app-py-image $registry:$BUILD_NUMBER'
            }
        }
        stage('Pushing image to docker repository') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'DOCKERHUB-ACCESS-TOKEN', passwordVariable: 'DOCKER_HUB_PASSWORD', usernameVariable: 'DOCKER_HUB_USERNAME')]) {
                    sh "echo $DOCKER_HUB_PASSWORD | docker login -u $DOCKER_HUB_USERNAME --password-stdin"
                    sh 'docker push $registry:$BUILD_NUMBER'
                    sh 'docker push $registry:latest'
                }
            }
        }
    }
        post {
            success {
                slackSend channel: 'jenkins', message: "Build deployed successfully - $JOB_NAME $BUILD_NUMBER $BUILD_URL"
            }
            failure {
                slackSend channel: 'jenkins', failOnError:true, message: "Build failed  - $JOB_NAME $BUILD_NUMBER $BUILD_URL"
            }
        }
    
}

