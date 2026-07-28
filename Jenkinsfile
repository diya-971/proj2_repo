pipeline {
    agent any

    environment {
        IMAGE_NAME = "diyapaul/ipstpro-website"
    }

    stages {

        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t ${IMAGE_NAME}:latest .
                '''
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {

                    sh '''
                    echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                docker push ${IMAGE_NAME}:latest
                '''
            }
        }

        stage('Docker Logout') {
            steps {
                sh 'docker logout'
            }
        }
    }

    post {

        success {
            echo 'Docker image successfully pushed.'

            mail(
                to: 'pdiya971@gmail.com',
                subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Hello,

Your Jenkins pipeline completed successfully.

Job Name      : ${env.JOB_NAME}
Build Number  : ${env.BUILD_NUMBER}
Build Status  : SUCCESS

Docker Image:
${IMAGE_NAME}:latest

Build URL:
${env.BUILD_URL}

Regards,
Jenkins
"""
            )
        }

        failure {
            echo 'Pipeline failed.'

            mail(
                to: 'pdiya971@gmail.com',
                subject: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Hello,

Your Jenkins pipeline failed.

Job Name      : ${env.JOB_NAME}
Build Number  : ${env.BUILD_NUMBER}
Build Status  : FAILED

Please check the Jenkins console output.

Build URL:
${env.BUILD_URL}

Regards,
Jenkins
"""
            )
        }

        always {
            sh 'docker images'
        }
    }
}
