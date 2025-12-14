pipeline {
    agent any

    environment {
        BUCKET_NAME = "shubhamwebsitewithawss3"
        AWS_DEFAULT_REGION = "ap-south-1"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-creds',
                    url: 'https://github.com/khisteshubham/aws-terraform-cicd-app.git'
            }
        }

        stage('Build Application') {
            steps {
                echo 'Building static website...'
                bat 'echo Build completed'
            }
        }

        stage('Deploy to S3') {
            steps {
                withAWS(credentials: 'aws-s3-creds', region: 'ap-south-1') {
                    bat '''
                    aws s3 sync . s3://%BUCKET_NAME% --delete ^
                      --exclude ".git/*" ^
                      --exclude ".terraform/*" ^
                      --exclude "*.tf" ^
                      --exclude "Jenkinsfile"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment to S3 successful '
        }
        failure {
            echo 'Deployment failed ❌'
        }
    }
}
