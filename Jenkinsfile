pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup Python') {
            steps {
                sh 'python3 --version'
                sh 'pip3 --version'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate

                    python -m pip install --upgrade pip
                    python -m pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    . venv/bin/activate
                    pytest
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    . venv/bin/activate

                    export BUILD_ID=dontKillMe

                    nohup python app.py > flask.log 2>&1 &
                '''
            }
        }
    }

    post {

        success {
            emailext(
                to: "YOUR-EMAIL-ADDRESS",
                subject: "✅ SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Hello,

Your Jenkins pipeline completed successfully.

----------------------------------------
Project : ${env.JOB_NAME}
Build   : #${env.BUILD_NUMBER}
Status  : SUCCESS
Branch  : ${env.BRANCH_NAME ?: "main"}

Build URL:
${env.BUILD_URL}

Regards,
Jenkins CI/CD
"""
            )
        }

        failure {
            emailext(
                to: "YOUR-EMAIL-ADDRESS",
                subject: "❌ FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Hello,

Your Jenkins pipeline has FAILED.

----------------------------------------
Project : ${env.JOB_NAME}
Build   : #${env.BUILD_NUMBER}
Status  : FAILED

Please review the build logs:

${env.BUILD_URL}

Regards,
Jenkins CI/CD
"""
            )
        }
    }
}