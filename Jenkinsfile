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
    post {
    success {
        emailext(
            subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: """
            <h2>Build Successful</h2>

            <p>Job: ${env.JOB_NAME}</p>

            <p>Build Number: ${env.BUILD_NUMBER}</p>

            <p>Image successfully pushed to Docker Hub.</p>

            <p>Build URL: ${env.BUILD_URL}</p>
            """,
            to: "your-email@gmail.com"
        )
    }

    failure {
        emailext(
            subject: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: """
            <h2>Build Failed</h2>

            <p>Job: ${env.JOB_NAME}</p>

            <p>Build Number: ${env.BUILD_NUMBER}</p>

            <p>Please check Jenkins logs.</p>

            <p>${env.BUILD_URL}</p>
            """,
            to: "your-email@gmail.com"
        )
    }
}

}

