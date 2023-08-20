pipeline {
    tools {
        maven 'Maven3'
    }
    agent any

    environment {
        registry = "353928175117.dkr.ecr.us-east-1.amazonaws.com/my-jenkins-ecr"
    }

    stages {
        stage('Git checkout') {
            steps {
               checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/obusorezekiel/cicd-jenkins.git']])
            }
        }

        stage('Build Artifact') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
               script{
                   dockerImage = docker.build registry
                   dockerImage.tag("$BUILD_NUMBER")
               }
            }
        }

        stage('Push Docker Image') {
            steps {
               script{
                   sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 353928175117.dkr.ecr.us-east-1.amazonaws.com'
                   sh'docker push 353928175117.dkr.ecr.us-east-1.amazonaws.com/my-jenkins-ecr:$BUILD_NUMBER'
               }
            }
        }

        stage('Helm Deployment') {
            steps {
               script{
                   sh"helm upgrade first --install mychart --namespace helm-deployment --set image.tag=$BUILD_NUMBER"
                  
               }
            }
        }

    }
}