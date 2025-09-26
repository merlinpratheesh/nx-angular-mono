pipeline {
    agent any

    parameters {
        choice(name: 'APP_NAME', choices: ['admin', 'buyer', 'seller'], description: 'Select app to deploy')
    }

    environment {
        IMAGE_NAME = "mycompany/${params.APP_NAME}:latest"
    }

    stages {

        // 1️⃣ Checkout Code from Git
        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/merlinpratheesh/nx-angular-mono.git'
            }
        }

        // 2️⃣ Install dependencies & Build Angular app
        stage('Install & Build Angular') {
            steps {
                script {
                    sh 'npm ci'  // Install dependencies
                    sh "npx nx build ${params.APP_NAME} --prod" // Build selected app
                }
            }
        }

        // 3️⃣ Build Docker image
        stage('Build & Dockerize') {
            steps {
                script {
                    sh "docker build --build-arg APP_NAME=${params.APP_NAME} -t ${IMAGE_NAME} ."
                }
            }
        }

        // 4️⃣ Push Docker image
        stage('Push Image') {
            steps {
                script {
                    sh "docker push ${IMAGE_NAME}"
                }
            }
        }

        // 5️⃣ Deploy container
        stage('Deploy') {
            steps {
                script {
                    // Stop existing container if running
                    sh "docker rm -f ${params.APP_NAME} || true"
                    // Run new container
                    sh "docker run -d --name ${params.APP_NAME} -p ${getPort(params.APP_NAME)}:80 ${IMAGE_NAME}"
                }
            }
        }
    }
}

// Function to assign ports per app
def getPort(appName) {
    if (appName == 'admin') return 8081
    if (appName == 'buyer') return 8082
    if (appName == 'seller') return 8083
}
