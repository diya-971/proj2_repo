pipeline {

    agent any

    environment {

        IMAGE_NAME = "diya971/ipstpro-website"

    }

    stages {

        stage('Clone') {

            steps {

                git 'https://github.com/diya-971/proj2_repo.git'

            }

        }

        stage('Build Image') {

            steps {

                sh 'docker build -t $IMAGE_NAME:latest .'

            }

        }

        stage('Docker Login') {

            steps {

                withCredentials([usernamePassword(credentialsId: 'dockerhub',
                                usernameVariable: 'USERNAME',
                                passwordVariable: 'PASSWORD')]) {

                    sh '''
                    echo $PASSWORD | docker login -u $USERNAME --password-stdin
                    '''

                }

            }

        }

        stage('Push Image') {

            steps {

                sh 'docker push $IMAGE_NAME:latest'

            }

        }

    }

    post {

        success {

            echo "Image Successfully Pushed to Docker Hub"

        }

        failure {

            echo "Pipeline Failed"

        }

    }

}
