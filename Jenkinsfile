pipeline {
    agent any

    environment {
        GIT_CREDENTIALS = 'github-creds'
    }

    stages {

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

        stage('Move & Push Package to GitHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'github-creds',
                    usernameVariable: 'GIT_USERNAME',
                    passwordVariable: 'GIT_TOKEN'
                )]) {
                    sh '''
                    # Move package files
                    mv dist/*.whl .
                    mv dist/*.tar.gz .

                    # Git config
                    git config --global user.name "$GIT_USERNAME"
                    git config --global user.email "jenkins@example.com"

                    # Add files
                    git add *.whl *.tar.gz

                    # Commit
                    git commit -m "Added built package [CI]" || echo "No changes"

                    # Push securely using credentials
                    git push https://$GIT_USERNAME:$GIT_TOKEN@github.com/shanifm2002/python-pipeline.git HEAD:main
                    '''
                }
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