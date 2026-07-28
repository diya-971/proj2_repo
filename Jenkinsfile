pipeline {

    agent any

    environment {
        IMAGE_NAME = "diyapaul/ipstpro-website"
    }

    stages {

        stage('Build Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'USERNAME',
                    passwordVariable: 'PASSWORD'
                )]) {

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

        stage('Docker Logout') {
            steps {
                sh 'docker logout'
            }
        }
    }

    post {

        success {

            echo "Image Successfully Pushed to Docker Hub"

            emailext(
                subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                mimeType: 'text/html',
                body: """
                <h2>Build Successful</h2>

                <p><b>Job:</b> ${env.JOB_NAME}</p>

                <p><b>Build Number:</b> ${env.BUILD_NUMBER}</p>

                <p><b>Docker Image:</b> ${IMAGE_NAME}:latest</p>

                <p>Image successfully pushed to Docker Hub.</p>

                <p><a href="${env.BUILD_URL}">Open Build</a></p>
                """,
                to: "pdiya971@gmail.com"
            )
        }

        failure {

            echo "Pipeline Failed"

            emailext(
                subject: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                mimeType: 'text/html',
                body: """
                <h2>Build Failed</h2>

                <p><b>Job:</b> ${env.JOB_NAME}</p>

                <p><b>Build Number:</b> ${env.BUILD_NUMBER}</p>

                <p>Please check the Jenkins console output.</p>

                <p><a href="${env.BUILD_URL}">Open Build</a></p>
                """,
                to: "pdiya971@gmail.com"
            )
        }

        always {
            sh 'docker images'
        }
    }
}
