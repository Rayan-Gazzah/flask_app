pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub')
        DOCKER_IMAGE = 'rayan-gazzah/flask_app'
    }

    stages {
        stage('Setup Environment') {
            steps {
                bat 'python -m pip install --quiet --requirement requirements.txt'
            }
        }

        stage('Linting') {
            steps {
                script {
                    bat 'python -m flake8 --ignore=E501,E231 *.py'
                    bat 'python -m pylint --disable=C0301 --disable=C0326 test_app.py'
                }
            }
        }

        stage('Unit Testing') {
            steps {
                bat 'python -m unittest --verbose --failfast'
            }
        }

        stage('Build') {
            steps {
                bat 'python -m pip install --quiet --requirement requirements.txt'
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
                bat 'kubectl apply -f k8s/deployment.yml'
            }
        }
    }
}
