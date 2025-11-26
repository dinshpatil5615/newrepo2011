pipeline {
    agent any

    environment {
        IMAGE = "dineshpatil0908/python-cicd"
        TARGET_USER = "azureuser"
        TARGET_HOST = "134.149.97.100"
    }

    stages {

        stage("Checkout") {
            steps {
                git branch: 'main',
                    url: 'https://github.com/dinshpatil5615/newrepo2011.git'
            }
        }

        stage("Build Docker Image") {
            steps {
                sh """
                    docker build -t dineshpatil0908/ci-cd-python:latest .
                """
            }
        }

        stage("DockerHub Login") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PSW')]) {
                    sh '''
                        echo $DOCKERHUB_PSW | docker login -u $DOCKERHUB_USER --password-stdin
                    '''
                }
            }
        }

		stage("Push to DockerHub") {
    		steps {
        		sh "docker push dineshpatil0908/ci-cd-python:latest"
   	 			}
			}

        stage("Deploy to Target") {
            steps {
                sh """
                    ssh -o StrictHostKeyChecking=no ${TARGET_USER}@${TARGET_HOST} '
                        docker pull ${IMAGE}:latest;
                        docker rm -f flask-app || true;
                        docker run -d --name flask-app -p 5000:5000 ${IMAGE}:latest;
                    '
                """
            }
        }
    }
}
