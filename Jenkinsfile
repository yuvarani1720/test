pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'master',
                url: 'https://github.com/yuvarani1720/test'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                scp -r * ec2-user@13.232.202.46:/opt/demo-app/
                '''
            }
        }

        stage('Start Application') {
            steps {
                sh '''
                ssh ec2-user@13.232.202.46 '
                cd /opt/demo-app

                npm install

                pkill node || true

                nohup node app.js > app.log 2>&1 &
                '
                '''
            }
        }
    }
}
