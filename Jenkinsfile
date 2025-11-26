pipeline {
    agent {
        docker {
            image 'python:3.9-slim'
        }
    }

    stages {
        stage('Setup Python') {
            steps {
                echo 'Checking Python installation in Docker container...'
                sh '''
                    which python
                    python --version
                    which pip
                    pip --version
                '''
            }
        }
        stage('Build') {
            steps {
                echo 'Creating virtual environment and installing dependencies...'
                sh 'python -m venv venv'
                sh 'source venv/bin/activate && pip install -r requirements.txt'
            }
        }
        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'source venv/bin/activate && python -m unittest discover -s .'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                sh '''
                mkdir -p ${WORKSPACE}/python-app-deploy
                cp ${WORKSPACE}/app.py ${WORKSPACE}/python-app-deploy/
                cp ${WORKSPACE}/requirements.txt ${WORKSPACE}/python-app-deploy/
                '''
            }
        }
        stage('Run Application') {
            steps {
                echo 'Running application...'
                sh '''
                cd ${WORKSPACE}/python-app-deploy
                python -m venv venv
                source venv/bin/activate
                pip install -r requirements.txt
                nohup python app.py > app.log 2>&1 & 
                echo $! > app.pid
                '''
            }
        }
        stage('Test Application') {
            steps {
                echo 'Testing deployed application...'
                sh '''
                cd ${WORKSPACE}/python-app-deploy
                source venv/bin/activate
                python ../test_app.py
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs.'
        }
    }
}
