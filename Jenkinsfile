pipeline {
    agent any

    tools {
        nodejs 'node'
    }

    options {
        skipDefaultCheckout(true)
    }

    environment {
        IMAGE_MAIN = "nodemain:v1.0"
        IMAGE_DEV  = "nodedev:v1.0"
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Set logo & port') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh "cp src/logo-main.svg src/logo.svg"
                        env.PORT = '3000'
                        env.IMAGE = env.IMAGE_MAIN
                    } else if (env.BRANCH_NAME == 'dev') {
                        sh "cp src/logo-dev.svg src/logo.svg"
                        env.PORT = '3001'
                        env.IMAGE = env.IMAGE_DEV
                    } else {
                        error "Unsupported branch ${env.BRANCH_NAME}"
                    }
                }
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Docker build') {
            steps {
                sh "docker build -t ${env.IMAGE} ."
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh """
                        docker rm -f app-${BRANCH_NAME} || true

                        docker run -d --name app-${BRANCH_NAME} -p ${PORT}:3000 ${IMAGE}
                    """
                }
            }
        }
    }
}
