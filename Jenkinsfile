pipeline {
    agent any

    tools {
        maven 'Maven-3.9'
    }

    environment {
        DOCKERHUB_REPO = 'douaesr/hello-spring'
        CONTAINER_NAME = 'hello-app'
        APP_PORT = '8070'
    }

    stages {

        stage('Build JAR') {
            steps {
                echo '🔨 Compilation de l application...'
                sh 'mvn clean package'
            }
        }

        stage('Build & Push Docker') {
            steps {
                echo '🐳 Construction et publication de l image Docker...'
                script {
                    // Change 'dockerhub' to 'docker-hub'
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub') {
                        def app = docker.build("${DOCKERHUB_REPO}:latest")
                        app.push()
                    }
                }
            }
        }

stage('Deploy') {
    steps {
        echo '🚀 Déploiement de l application...'
        script {
            docker.withRegistry('https://index.docker.io/v1/', 'docker-hub') {
                sh """
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                    docker pull ${DOCKERHUB_REPO}:latest
                    docker run -d \
                      --name ${CONTAINER_NAME} \
                      -p ${APP_PORT}:${APP_PORT} \
                      ${DOCKERHUB_REPO}:latest
                """
            }
        }
    }
}

    post {
        success {
            echo '✅ Pipeline exécuté avec succès!'
        }
        failure {
            echo '❌ Le pipeline a échoué.'
        }
    }
}
