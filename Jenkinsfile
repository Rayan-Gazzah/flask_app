pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub')
        DOCKER_IMAGE = 'rayan07/flask_app'
        PYTHON_PATH = 'C:\\Users\\msi\\AppData\\Local\\Programs\\Python\\Python310\\python.exe'
        KUBECONFIG_FILE = credentials('k8s-config')
    }

    stages {
        stage('Setup Environment') {
            steps {
                bat '"%PYTHON_PATH%" -m pip install --quiet --requirement requirements.txt'
            }
        }

        stage('Linting') {
            steps {
                script {
                    bat '"%PYTHON_PATH%" -m flake8 --ignore=E501,E231 *.py'
                    bat '"%PYTHON_PATH%" -m pylint --disable=C0301 --disable=C0326 test_app.py'
                }
            }
        }

        stage('Unit Testing') {
            steps {
                bat '"%PYTHON_PATH%" -m unittest --verbose --failfast'
            }
        }

        stage('Build') {
            steps {
                bat '"%PYTHON_PATH%" -m pip install --quiet --requirement requirements.txt'
            }
        }

        stage('Release') {
            steps {
                script {
                    bat 'docker build -t %DOCKER_IMAGE% .'
                    bat 'docker login -u %DOCKER_HUB_CREDENTIALS_USR% -p %DOCKER_HUB_CREDENTIALS_PSW%'
                    bat 'docker push %DOCKER_IMAGE%'
                }
            }
        }

        stage('Deploy') {
            steps {
                withEnv(["KUBECONFIG=%KUBECONFIG_FILE%"]) {
                    bat 'kubectl apply -f deployment.yml'
                }
            }
        }
    }
}
