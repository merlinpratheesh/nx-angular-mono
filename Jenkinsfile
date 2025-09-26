pipeline {
    agent any

    parameters {
        choice(name: 'APP_NAME', choices: ['admin', 'buyer', 'seller'], description: 'Select app to deploy')
    }

    environment {
        IMAGE_NAME = "nxDockerDeploy/${params.APP_NAME}:latest"
        WORKSPACE_ROOT = "${WORKSPACE}"
        DIST_DIR = "${WORKSPACE}\\dist\\apps\\${params.APP_NAME}"
    }

    stages {

        // 1️⃣ Checkout Code from Git
        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/merlinpratheesh/nx-angular-mono.git'
            }
        }

        // 2️⃣ Install dependencies (workspace root)
        stage('Install Dependencies') {
            steps {
                dir("${WORKSPACE_ROOT}") {
                    bat "npm install"
                }
            }
        }

        // 3️⃣ Build selected Angular app
        stage('Build Angular App') {
            steps {
                dir("${WORKSPACE_ROOT}") {
                    bat "npx nx build ${params.APP_NAME} --configuration=production"
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
