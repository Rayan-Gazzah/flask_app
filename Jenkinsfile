pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub')
        DOCKER_IMAGE = 'rayan-gazzah/flask_app'
    }

    stages {
        stage('Linting') {
            steps {
                script {
                    sh 'flake8 --ignore=E501,E231 *.py'
                    sh 'pylint --disable=C0301 --disable=C0326 test_app.py'
                }
            }
        }

        stage('Unit Testing') {
            steps {
                sh 'python -m unittest --verbose --failfast'
            }
        }

        stage('Build') {
            steps {
                sh 'pip install --quiet --requirement requirements.txt'
            }
        }

        stage('Release') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_IMAGE .'
                    sh 'docker login -u $DOCKER_HUB_CREDENTIALS_USR -p $DOCKER_HUB_CREDENTIALS_PSW'
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'kubectl apply -f k8s/deployment.yml'
            }
        }
    }
}
