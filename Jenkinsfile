pipeline {
    agent any

    tools { nodejs "nodejs20" }

    environment {
        STAGE = "dev"
    }

    stages {
        stage('Install Dependencies') {
            steps {
                // Instalar dependencias
                sh 'npm install'
            }
        }

        stage('Deploy to AWS') {
            when {
                anyOf {
                    allOf {
                        branch 'dev'
                        environment name: "STAGE", value: "dev"
                    }
                    allOf {
                        branch 'main'
                        environment name: "STAGE", value: "main"
                    }
                }
            }
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        env.AWS_ACCESS_KEY_ID = input message: 'Please enter AWS Access Key ID:', parameters: [string(defaultValue: '', description: 'AWS Access Key ID', name: 'AWS_ACCESS_KEY_ID')]
                        env.AWS_SECRET_ACCESS_KEY = input message: 'Please enter AWS Secret Access Key:', parameters: [string(defaultValue: '', description: 'AWS Secret Access Key', name: 'AWS_SECRET_ACCESS_KEY')]
                    } else {
                        env.AWS_ACCESS_KEY_ID = credentials('aws_access_key_id')
                        env.AWS_SECRET_ACCESS_KEY = credentials('aws_secret_access_key')
                    }
                }
                // Configurar credenciales de AWS
                withEnv(["AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}", "AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}"]) {
                    // Desplegar con Serverless Framework
                    sh 'npx serverless deploy --stage ${STAGE} --region us-east-2'
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
