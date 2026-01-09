pipeline {
agent any
environment {
DOCKERHUB_REPO = 'douaesr/hello-spring'
CONTAINER_NAME = 'hello-app'
}

stages {
stage('Build JAR') {
steps {
echo '🔨Compilation de l application...'
sh 'mvn clean package'
}
}
stage('Build & Push Docker') {
steps {
echo '🐳Construction et publication de l image Docker...'
script {
docker.withRegistry('', 'dockerhub') {
def app = docker.build("${DOCKERHUB_REPO}:latest")
app.push()
}
}
}
}
stage('Deploy') {
steps {
echo '🚀Déploiement de l application...'
sh """
docker stop ${CONTAINER_NAME} || true
docker rm ${CONTAINER_NAME} || true
docker pull ${DOCKERHUB_REPO}:latest
docker run -d --name ${CONTAINER_NAME} -p 8080:8080 ${DOCKERHUB_REPO}:latest
"""
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