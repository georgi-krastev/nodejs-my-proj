pipeline {

    agent {
        label 'my-ssh-agent-2'
    }

    tools {
        nodejs 'nodejs'
    }
    
    triggers {
        githubPush()
        dockerTool 'docker'
    }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('my_dockerhub_credentials')
        IMAGE_NAME = 'georgikrastev/mynodejsapp'
    }

    stages {

        stage('Clean') {
            steps {
                cleanWs()
            }
        }

        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/georgi-krastev/nodejs-my-proj.git'
            }
        }
        stage('Build') {
            steps {
                sh 'npm ci' //This is for building the nodejs project
            }
        }
        stage('Test') {
            steps {
                sh 'npm test' //This is for testing the nodejs modules
            }
        }
        stage('Docker login'){
            steps {
               sh 'docker login -u $DOCKERHUB_CREDENTIALS -p $DOCKERHUB_CREDENTIALS'
            }

        }

        stage('Docker build and tag') {
            steps {
                sh 'docker build -t ${IMAGE_NAME} -f Dockerfile .'
                sh 'docker tag ${IMAGE_NAME} ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }

        stage('Docker Push') {
            steps {
                sh 'docker push ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
        stage('Deploy') {
           steps {
                sh 'pkill node | true'
                sh 'npm install -g forever'
                sh 'forever start src/index.js'
           }
        }
    }
}
