pipeline {
    agent any

    tools {nodejs "nodejs"}

    environment {
        // Definir variables de entorno para AWS
        AWS_ACCESS_KEY_ID = ''
        AWS_SECRET_ACCESS_KEY = ''
    }

    stages {
        stage('Input Credentials') {
            steps {
                script {
                    // Solicitar AWS Access Key y Secret Key
                    AWS_ACCESS_KEY_ID = input message: 'Ingrese su AWS Access Key ID', parameters: [string(defaultValue: '', description: 'AWS Access Key ID', name: 'AWS_ACCESS_KEY_ID')]
                    AWS_SECRET_ACCESS_KEY = input message: 'Ingrese su AWS Secret Access Key', parameters: [password(defaultValue: '', description: 'AWS Secret Access Key', name: 'AWS_SECRET_ACCESS_KEY')]
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                // Instalar dependencias
                sh 'npm install'
            }
        }

        stage('Deploy to AWS') {
            steps {
                // Configurar credenciales de AWS
                withEnv(["AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}", "AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}"]) {
                    // Desplegar con Serverless Framework
                    sh 'npx serverless deploy --stage dev --region us-east-2'
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
