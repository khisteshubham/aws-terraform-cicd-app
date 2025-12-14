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
        withCredentials([
            string(credentialsId: 'aws-access-key-id', variable: 'AWS_ACCESS_KEY_ID'),
            string(credentialsId: 'aws-secret-access-key', variable: 'AWS_SECRET_ACCESS_KEY')
        ]) {
            bat '''
            set AWS_DEFAULT_REGION=ap-south-1
            aws s3 sync . s3://shubhamwebsitewithawss3 --delete ^
              --exclude ".git/*" ^
              --exclude ".terraform/*" ^
              --exclude "*.tf" ^
              --exclude "Jenkinsfile"
            '''
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
