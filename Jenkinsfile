pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO = 'psanjayk04/ecommerce-app'   // Your Docker Hub repo
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo '📦 Cloning repository from GitHub...'
                git branch: 'master', url: 'https://github.com/psanjayk04/CI-Project.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo '📦 Installing Node.js dependencies...'
                sh 'npm install'
            }
        }

        stage('Code Analysis - SonarQube') {
            steps {
                echo '🔍 Running SonarQube static code analysis...'
                withSonarQubeEnv('SonarQube') {
                    sh '''
                        sonar-scanner \
                          -Dsonar.projectKey=ecommerce-app \
                          -Dsonar.projectName="E-Commerce Application" \
                          -Dsonar.sources=server,public \
                          -Dsonar.host.url=$SONAR_HOST_URL \
                          -Dsonar.login=$SONAR_AUTH_TOKEN
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🐳 Building Docker image...'
                script {
                    def IMAGE_TAG = "${BUILD_NUMBER}"
                    sh """
                        docker build -t ${DOCKER_HUB_REPO}:${IMAGE_TAG} .
                        docker tag ${DOCKER_HUB_REPO}:${IMAGE_TAG} ${DOCKER_HUB_REPO}:latest
                    """
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo '🚀 Pushing image to Docker Hub...'
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push manjukolkar007/ecommerce-app:${BUILD_NUMBER}
                        docker push manjukolkar007/ecommerce-app:latest
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ CI pipeline completed successfully!'
        }
        failure {
            echo '❌ CI pipeline failed. Please check logs above.'
        }
    }
}
