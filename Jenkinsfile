pipeline {
    agent any

    environment {
        PYTHON_ENV = "myenv"
        PACKAGE_NAME = "jenkins_python_demo"
        REPO_URL = "https://pypi.pkg.github.com/shanifm2002/"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/shanifm2002/python-pipeline.git'
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
                    --repository-url $REPO_URL \
                    -u shanifm2002 \
                    -p $GH_TOKEN \
                    dist/*
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}