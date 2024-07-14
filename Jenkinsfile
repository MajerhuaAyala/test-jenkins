pipeline {
    agent any

    tools { nodejs "nodejs20" }

    environment {
        STAGE = "default"
    }

    stages {
        stage('Set Environment Variables') {
            steps {
                script {
                    // Cambiar el valor de la variable de entorno `STAGE` basado en el branch
                    if (env.BRANCH_NAME == 'main') {
                        env.STAGE = 'production'
                    } else if (env.BRANCH_NAME == 'dev') {
                        env.STAGE = 'development'
                    } else {
                        env.STAGE = 'unknown'
                    }

                    echo "BRANCH_NAME: ${env.BRANCH_NAME}"

                    // Imprimir el valor de STAGE para confirmar que ha cambiado
                    echo "The STAGE variable is set to: ${env.STAGE}"
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
                script {
                    // Cambiar el valor de STAGE basado en el branch
                    if (env.BRANCH_NAME == 'main') {
                        env.AWS_ACCESS_KEY_ID = input message: 'Please enter AWS Access Key ID:', parameters: [string(defaultValue: '', description: 'AWS Access Key ID', name: 'AWS_ACCESS_KEY_ID')]
                        env.AWS_SECRET_ACCESS_KEY = input message: 'Please enter AWS Secret Access Key:', parameters: [string(defaultValue: '', description: 'AWS Secret Access Key', name: 'AWS_SECRET_ACCESS_KEY')]
                    } else if (env.BRANCH_NAME == 'dev') {
                        env.AWS_ACCESS_KEY_ID = credentials('aws_access_key_id')
                        env.AWS_SECRET_ACCESS_KEY = credentials('aws_secret_access_key')
                    } else {
                        error "Branch ${env.BRANCH_NAME} is not a valid deployment branch"
                    }

                    // Imprimir el valor de STAGE para confirmar que ha cambiado
                    echo "STAGE is set to: ${STAGE}"
                }
                withEnv(["AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}", "AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}"]) {
                    // Desplegar con Serverless Framework
                    sh "npx serverless deploy --stage ${STAGE} --region us-east-2"
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
