pipeline {
agent any

```
environment {
    VENV = "myenv"
    PYTHON = "python3"
}

stages {

    stage('Checkout Code') {
        steps {
            git branch: 'main',
                url: 'https://github.com/shanifm2002/python-pipeline.git'
        }
    }

    stage('Setup Python & Install Dependencies') {
        steps {
            sh '''
            $PYTHON -m venv $VENV
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
            python src/app.py
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
            withCredentials([string(credentialsId: 'github-token', variable: 'GH_TOKEN')]) {
                sh '''
                . $VENV/bin/activate

                python -m twine upload \
                  --repository-url https://pypi.pkg.github.com/shanifm2002/python-pipeline/ \
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
        echo '✅ Pipeline succeeded!'
    }
    failure {
        echo '❌ Pipeline failed!'
    }
}
```

}
