pipeline {
    agent any

    environment {
        // DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials') // if needed later
        DOCKER_IMAGE = "employeeprofilemanagement_image"
        DB_URL = "jdbc:postgresql://host.docker.internal:5432/epms_db"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/HariharaJujjarapu/employeeprofilemanagement'
            }
        }

        stage('Build') {
            steps {
                sh '''
                    echo "🚀 Building Docker image..."
                    docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} -f Dockerfile .
                '''
            }
        }

        stage('Run Container') {
            steps {
                script {
                    sh '''
                        echo "🧹 Checking if container already exists..."
                        # Stop and remove container if it exists
                        if [ "$(docker ps -aq -f name=employeeprofilemanagement)" ]; then
                            echo "⚠️ Container already exists. Stopping and removing..."
                            docker stop employeeprofilemanagement || true
                            docker rm employeeprofilemanagement || true
                        fi

                        echo "🐳 Running new Docker container..."
                        docker run -e DB_URL=${DB_URL} -d --name employeeprofilemanagement -p 8200:8200 ${DOCKER_IMAGE}:${BUILD_NUMBER}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Checkout, Build, Dockerize & Deploy completed successfully!"
        }
        failure {
            echo "❌ Build failed!"
        }
    }
}
