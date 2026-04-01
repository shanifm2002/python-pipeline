pipeline {
    agent any

    stages {

        stage('Install Dependencies') {
            steps {
                sh '''
                python3 -m venv myenv
                . myenv/bin/activate
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run App') {
            steps {
                sh '''
                . myenv/bin/activate
                python3 src/app.py
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                . myenv/bin/activate
                pytest
                '''
            }
        }

    }
}