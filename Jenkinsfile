pipeline {
    agent any

    environment {
        TARGET_HOST = "13.232.202.46"
        TARGET_USER = "ec2-user"
        TARGET_PATH = "/opt/demo-app"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/yuvarani1720/test'
            }
        }

        stage('Debug') {
            steps {
                sh '''
                echo "===== DEBUG INFO ====="
                whoami
                echo "HOME=$HOME"
                pwd

                echo "===== SSH DIRECTORY ====="
                ls -la ~/.ssh || true

                echo "===== KNOWN HOSTS ====="
                cat ~/.ssh/known_hosts || true
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                mkdir -p ~/.ssh

                ssh-keyscan -H ${TARGET_HOST} >> ~/.ssh/known_hosts 2>/dev/null

                ssh -o StrictHostKeyChecking=no ${TARGET_USER}@${TARGET_HOST} "
                    mkdir -p ${TARGET_PATH}
                "

                scp -o StrictHostKeyChecking=no \
                    -r app.js package.json Jenkinsfile \
                    ${TARGET_USER}@${TARGET_HOST}:${TARGET_PATH}/
                '''
            }
        }

        stage('Start Application') {
            steps {
                sh '''
                ssh -o StrictHostKeyChecking=no ${TARGET_USER}@${TARGET_HOST} '

                echo "===== NODE VERSION ====="
                node -v

                echo "===== NPM VERSION ====="
                npm -v

                cd /opt/demo-app

                npm install || true

                pkill node || true

                nohup node app.js > app.log 2>&1 &

                sleep 5

                ps -ef | grep node

                '
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                ssh -o StrictHostKeyChecking=no ${TARGET_USER}@${TARGET_HOST} '

                echo "===== APP LOG ====="

                tail -20 /opt/demo-app/app.log || true

                '
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully'
        }

        failure {
            echo 'Deployment failed'
        }
    }
}
