pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/shanifm2002/python-pipeline.git'
            }
        }

        stage('Setup Python & Install Dependencies') {
            steps {
                sh '''
                python3 -m venv myenv
                . myenv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Application') {
            steps {
                sh '''
                . myenv/bin/activate
                export PYTHONPATH=.
                python3 src/app.py
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                . myenv/bin/activate
                export PYTHONPATH=.
                pytest
                '''
            }
        }

        stage('Package') {
            steps {
                sh '''
                . myenv/bin/activate
                python3 setup.py sdist bdist_wheel
                '''
            }
        }

    }

    post {
        success {
            echo '✅ Pipeline executed successfully!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
    }
}