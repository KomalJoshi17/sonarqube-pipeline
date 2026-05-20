pipeline {

    agent {
        docker {
            image 'node:20-alpine'
            args '-u root'
        }
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
    }

    post {

        always {
            echo 'Pipeline completed!'
        }

        success {
            echo 'Build and tests completed successfully!'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}
