pipeline {
    agent any

    tools { nodejs "nodejs20" }

    stages {
        stage('Install Dependencies') {
            steps {
                // Instalar dependencias
                sh 'npm install'
            }
        }

        stage("Deploy AWS DEV") {
            when {
                branch 'dev'
            }

            environment {
                AWS_ACCESS_KEY_ID = credentials('aws_access_key_id')
                AWS_SECRET_ACCESS_KEY = credentials('aws_secret_access_key')
            }

            steps {
                withEnv(["AWS_ACCESS_KEY_ID=${env.AWS_ACCESS_KEY_ID}", "AWS_SECRET_ACCESS_KEY=${env.AWS_SECRET_ACCESS_KEY}"]) {
                    // Desplegar con Serverless Framework
                    sh "npx serverless deploy --stage ${env.BRANCH_NAME} --region us-east-2"
                }
            }
        }

        stage("Deploy AWS main") {
            when {
                branch 'main'
            }

            environment {
                AWS_ACCESS_KEY_ID = input message: 'Please enter AWS Access Key ID:', parameters: [string(defaultValue: '', description: 'AWS Access Key ID', name: 'AWS_ACCESS_KEY_ID')]
                AWS_SECRET_ACCESS_KEY = input message: 'Please enter AWS Secret Access Key:', parameters: [string(defaultValue: '', description: 'AWS Secret Access Key', name: 'AWS_SECRET_ACCESS_KEY')]
            }

            steps {
                withEnv(["AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}", "AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}"]) {
                    // Desplegar con Serverless Framework
                    sh "npx serverless deploy --stage ${env.BRANCH_NAME} --region us-east-2"
                }
            }
            
        }

    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}
