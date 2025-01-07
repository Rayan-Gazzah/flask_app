pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub')
        DOCKER_IMAGE = 'rayan07/flask_app'
        PYTHON_PATH = 'C:\\Users\\msi\\AppData\\Local\\Programs\\Python\\Python310\\python.exe'
        KUBECONFIG_FILE = credentials('kubeconfig-file')  // Nom corrigé de l'ID du secret
    }

    stages {
        stage('Setup Environment') {
            steps {
                echo 'Setting up Python environment...'
                bat '"%PYTHON_PATH%" -m pip install --quiet --requirement requirements.txt'
            }
        }

        stage('Linting') {
            steps {
                echo 'Running Linting...'
                script {
                    bat '"%PYTHON_PATH%" -m flake8 --ignore=E501,E231 *.py'
                    bat '"%PYTHON_PATH%" -m pylint --disable=C0301 --disable=C0326 test_app.py'
                }
            }
        }

        stage('Unit Testing') {
            steps {
                echo 'Running Unit Tests...'
                bat '"%PYTHON_PATH%" -m unittest --verbose --failfast'
            }
        }

        stage('Build') {
            steps {
                echo 'Building the Docker image...'
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }

        stage('Release') {
            steps {
                echo 'Releasing Docker image to Docker Hub...'
                script {
                    bat 'docker login -u %DOCKER_HUB_CREDENTIALS_USR% -p %DOCKER_HUB_CREDENTIALS_PSW%'
                    bat 'docker push %DOCKER_IMAGE%'
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying to Kubernetes cluster...'
                withEnv(["KUBECONFIG=${KUBECONFIG_FILE}"]) {
                    bat 'kubectl apply -f deployment.yml'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
