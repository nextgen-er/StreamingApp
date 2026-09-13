def services = ['streaming-admin', 'streaming-auth', 'streaming-chat', 'streaming-frontend', 'streaming-stream']

pipeline {
    agent any
    environment {
        AWS_REGION = 'ap-south-1' // Change to your AWS region
        AWS_ACCOUNT_ID = '624504147976' // Change to your 12-digit AWS account ID
        ECR_REGISTRY = "${env.AWS_ACCOUNT_ID}.dkr.ecr.${env.AWS_REGION}.amazonaws.com"
    }
    stages {
        stage('AWS ECR Login & Push') {
            steps {
                // This wraps your build steps with your AWS credentials securely
                withCredentials([aws(credentialsId: 'ankit-aws-credentials',
                                     accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                                     secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh """
                        aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY}
                    """
                    script {
                        services.each { svc ->
                            def customImage = docker.build("${ECR_REGISTRY}/${svc}:${env.BUILD_NUMBER}", "./${svc}")
                            customImage.push()
                            customImage.push("latest")
                        }
                    }
                }
            }
        }
    }
}
