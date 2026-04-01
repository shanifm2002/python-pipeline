pipeline {
    agent any

    environment {
        PYTHON_ENV = 'myenv'
        REPO_URL = 'https://github.com/shanifm2002/python-pipeline.git'
        BRANCH = 'main'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: "${BRANCH}", url: "${REPO_URL}"
            }
        }

        stage('Setup Python & Install Dependencies') {
            steps {
                sh '''
                python3 -m venv $PYTHON_ENV
                . $PYTHON_ENV/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                pip install build twine pytest
                '''
            }
        }

        stage('Run Application') {
            steps {
                sh '''
                . $PYTHON_ENV/bin/activate
                export PYTHONPATH=.
                python3 src/app.py
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                . $PYTHON_ENV/bin/activate
                export PYTHONPATH=.
                pytest --maxfail=1 --disable-warnings -q
                '''
            }
        }

        stage('Build Package') {
            steps {
                sh '''
                . $PYTHON_ENV/bin/activate
                python -m build
                '''
            }
        }

        stage('Publish to GitHub Packages') {
            steps {
                withCredentials([string(credentialsId: 'github-token', variable: 'GH_TOKEN')]) {
                    sh '''
                    . $PYTHON_ENV/bin/activate
                    python -m twine upload \
                      --repository-url https://upload.pypi.org/legacy/ \
                      -u __token__ \
                      -p $GH_TOKEN \
                      dist/*
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline succeeded!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
    }
}