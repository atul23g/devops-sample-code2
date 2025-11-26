pipeline {
    agent any

    stages {
        stage('Setup Python') {
            steps {
                echo 'Checking Python installation...'
                sh '''
                    which python3 || echo "Python3 not found"
                    python3 --version || echo "Python3 version check failed"
                    which pip3 || echo "pip3 not found"
                    pip3 --version || echo "pip3 version check failed"
                '''
            }
        }
        stage('Build') {
            steps {
                echo 'Creating virtual environment and installing dependencies...'
                sh 'python3 -m venv venv'
                sh 'source venv/bin/activate && pip install -r requirements.txt'
            }
        }
        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'source venv/bin/activate && python3 -m unittest discover -s .'
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
                python3 -m venv venv
                source venv/bin/activate
                pip install -r requirements.txt
                nohup python3 app.py > app.log 2>&1 & 
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
                python3 ../test_app.py
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
