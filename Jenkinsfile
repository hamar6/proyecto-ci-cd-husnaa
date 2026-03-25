pipeline {
    agent any
    environment {
        DOCKER_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKER_IMAGE = 'husnaaama/python-app'
    }
    stages {
        stage('Clone') {
            steps {
                echo 'Clonando repositorio...'
                checkout scm
            }
        }
        stage('Test') {
            steps {
                echo 'Instalando dependencias y ejecutando tests...'
                sh 'pip3 install flask pytest --break-system-packages'
                sh 'python3 -m pytest test_app.py -v'
            }
        }
        stage('Build Image') {
            steps {
                echo 'Construyendo imagen Docker...'
                sh 'docker build -t ${DOCKER_IMAGE}:latest .'
            }
        }
        stage('DockerHub') {
            steps {
                echo 'Subiendo imagen a DockerHub...'
                sh '''
                    echo ${DOCKER_CREDENTIALS_PSW} | docker login -u ${DOCKER_CREDENTIALS_USR} --password-stdin
                    docker push ${DOCKER_IMAGE}:latest
                    docker logout
                '''
            }
        }
        stage('Deploy') {
            steps {
                echo 'Desplegando en Kubernetes...'
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }
    }
}
