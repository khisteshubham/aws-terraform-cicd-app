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
            REM Check if bucket exists
            aws s3 ls s3://%S3_BUCKET% >nul 2>&1
            if %errorlevel% NEQ 0 (
                echo Bucket does not exist. Creating...
                aws s3api create-bucket --bucket %S3_BUCKET% --region %AWS_DEFAULT_REGION% --create-bucket-configuration LocationConstraint=%AWS_DEFAULT_REGION%
                REM Enable static website hosting
                aws s3 website s3://%S3_BUCKET% --index-document index.html
                REM Make bucket public (optional, for static website)
                aws s3api put-bucket-policy --bucket %S3_BUCKET% --policy "{\\"Version\\":\\"2012-10-17\\",\\"Statement\\":[{\\"Effect\\":\\"Allow\\",\\"Principal\\":\\"*\\",\\"Action\\":\\"s3:GetObject\\",\\"Resource\\":\\"arn:aws:s3:::%S3_BUCKET%/*\\"}]}"
            ) else (
                echo Bucket exists. Skipping creation.
            )

            REM Deploy website
            aws s3 sync . s3://%S3_BUCKET% --delete ^
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
