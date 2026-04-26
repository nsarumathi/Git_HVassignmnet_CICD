pipeline {
    agent any

    environment {
        VENV = 'venv'
        EMAIL = 'sarumathinallasivam97@gmail.com'
    }

    triggers {
        githubPush()   // Auto trigger on GitHub push
    }

    stages {

        stage('Checkout') {
            steps {
               checkout scm
            }
        }

        stage('Build') {
            steps {
                sh '''
                python3 -m venv $VENV
                . $VENV/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                . $VENV/bin/activate
                pip install pytest
                pytest || exit 1
                '''
            }
        }

        stage('Deploy') {
            when {
                expression { currentBuild.currentResult == 'SUCCESS' }
            }
            steps {
                sh '''
                echo "Deploying to staging..."
                nohup python app.py > app.log 2>&1 &
                '''
            }
        }
    }

    post {
        success {
            echo 'Build SUCCESS'
            mail to: "${EMAIL}",
                 subject: "Jenkins SUCCESS: ${env.JOB_NAME}",
                 body: "Build succeeded! Check Jenkins."
        }

        failure {
            echo 'Build FAILED'
            mail to: "${EMAIL}",
                 subject: "Jenkins FAILED: ${env.JOB_NAME}",
                 body: "Build failed! Check logs immediately."
        }
    }
}
