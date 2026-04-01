pipeline {
    agent any

    environment {
        VENV = "myenv"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Setup Python & Install Dependencies') {
            steps {
                sh '''
                python3 -m venv $VENV
                . $VENV/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                pip install build twine pytest
                '''
            }
        }

        stage('Run Application') {
            steps {
                sh '''
                . $VENV/bin/activate
                export PYTHONPATH=.
                python3 src/app.py
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                . $VENV/bin/activate
                export PYTHONPATH=.
                pytest --maxfail=1 --disable-warnings -q
                '''
            }
        }

        stage('Build Package') {
            steps {
                sh '''
                . $VENV/bin/activate
                python -m build
                '''
            }
        }

        stage('Publish to GitHub Packages') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'github-creds',
                    usernameVariable: 'GH_USER',
                    passwordVariable: 'GH_TOKEN'
                )]) {
                    sh '''
                    . $VENV/bin/activate

                    python -m twine upload \
                      --repository-url https://upload.pypi.pkg.github.com/$GH_USER/ \
                      -u $GH_USER \
                      -p $GH_TOKEN \
                      dist/*
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Package published to GitHub Packages successfully!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
    }
}