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
            echo "Image Successfully Pushed to Docker Hub"

            emailext(
                to: "pdiya971@gmail.com",
                subject: "SUCCESS : ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                mimeType: 'text/html',
                body: """
                <html>
                <body>
                <h2 style="color:green;">Build Successful</h2>

                <table border="1" cellpadding="8">
                    <tr>
                        <th>Job Name</th>
                        <td>${env.JOB_NAME}</td>
                    </tr>
                    <tr>
                        <th>Build Number</th>
                        <td>${env.BUILD_NUMBER}</td>
                    </tr>
                    <tr>
                        <th>Status</th>
                        <td>SUCCESS</td>
                    </tr>
                    <tr>
                        <th>Docker Image</th>
                        <td>${IMAGE_NAME}:latest</td>
                    </tr>
                </table>

                <br>

                <a href="${env.BUILD_URL}">
                Open Jenkins Build
                </a>

                </body>
                </html>
                """
            )
        }

        failure {
            echo "Pipeline Failed"

            emailext(
                to: "pdiya971@gmail.com",
                subject: "FAILED : ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                mimeType: 'text/html',
                body: """
                <html>
                <body>
                <h2 style="color:red;">Build Failed</h2>

                <table border="1" cellpadding="8">
                    <tr>
                        <th>Job Name</th>
                        <td>${env.JOB_NAME}</td>
                    </tr>
                    <tr>
                        <th>Build Number</th>
                        <td>${env.BUILD_NUMBER}</td>
                    </tr>
                    <tr>
                        <th>Status</th>
                        <td>FAILED</td>
                    </tr>
                </table>

                <br>

                Please check the Jenkins Console Output.

                <br><br>

                <a href="${env.BUILD_URL}">
                Open Jenkins Build
                </a>

                </body>
                </html>
                """
            )
        }

        always {
            sh 'docker images'
        }
    }
}
