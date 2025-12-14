pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'ap-south-1'
        S3_BUCKET = 'shubhamwebsitewithawss3'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/khisteshubham/aws-terraform-cicd-app.git',
                    credentialsId: 'github-creds'
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
                    REM Deploy to S3 (bucket must exist or auto-create)
                    aws s3 ls s3://%S3_BUCKET% || aws s3api create-bucket --bucket %S3_BUCKET% --region %AWS_DEFAULT_REGION
                    aws s3 sync . s3://%S3_BUCKET% --delete ^
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
            echo 'Deployment successful 🚀'
        }
        failure {
            echo 'Deployment failed ❌'
        }
    }
}