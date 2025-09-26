pipeline {
    agent any

    parameters {
        choice(name: 'APP_NAME', choices: ['admin', 'buyer', 'seller'], description: 'Select app to deploy')
    }

    environment {
        IMAGE_NAME = "mycompany/${params.APP_NAME}:latest"
        APP_DIR   = "${WORKSPACE}\\apps\\${params.APP_NAME}"
    }

    stages {

        // 1️⃣ Checkout Code from Git
        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/merlinpratheesh/nx-angular-mono.git'
            }
        }

        // 2️⃣ Install dependencies for the selected app
        stage('Install Dependencies') {
            steps {
                dir("${APP_DIR}") {
                    bat "npm install"
                }
            }
        }

        // 3️⃣ Build Angular app for the selected app
        stage('Build Angular App') {
            steps {
                dir("${APP_DIR}") {
                    bat "npm run build -- --configuration=production"
                }
            }
        }

        // 4️⃣ Build Docker image
        stage('Build & Dockerize') {
            steps {
                bat "docker build --build-arg APP_NAME=${params.APP_NAME} -t ${IMAGE_NAME} ."
            }
        }

        // 5️⃣ Deploy container
        stage('Deploy') {
            steps {
                bat "docker rm -f ${params.APP_NAME} || exit 0"
                bat "docker run -d --name ${params.APP_NAME} -p ${getPort(params.APP_NAME)}:80 ${IMAGE_NAME}"
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
