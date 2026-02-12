pipeline {
    agent any

    environment {
        APP_DIR   = "/home/ubuntu/flask-app"
        VENV_DIR = "venv"
        FLASK_PORT = "5000"
        FLASK_EC2 = "ubuntu@35.154.184.112"
        // FLASK_EC2 = "ubuntu@NEW IP"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/shyam-sdr/Jenkins-ci-cd.git'
            }
        }

        stage('Install Dependencies (Jenkins Test)') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Basic Test') {
            steps {
                sh '''
                . venv/bin/activate
                python -c "import flask; print('Flask OK')"
                '''
            }
        }

        stage('Deploy to Flask EC2') {
            steps {
                sh '''
                # Ensure app directory exists
                ssh ${FLASK_EC2} "mkdir -p ${APP_DIR}"

                # Copy only required files (NO venv)
                #scp app.py requirements.txt dev_flask.sh -r ${FLASK_EC2}:${APP_DIR}
                scp -r app.py requirements.txt dev_flask.sh ${FLASK_EC2}:${APP_DIR}
                
                # Install deps and restart Flask cleanly
                ssh ubuntu@35.154.184.112 "cd /home/ubuntu/flask-app && \
                rm -rf venv && \
                python3 -m venv venv && \
                venv/bin/pip install --upgrade pip && \
                venv/bin/pip install -r requirements.txt && \
                chmod +x dev_flask.sh && \
                bash dev_flask.sh restart"
                "
                '''
            }
        }
    }
}


