pipeline {

    agent {
        docker {
            image 'node:20-alpine'
        }
    }

    environment {

        SONAR_HOST_URL = 'http://10.35.137.13:9000'

        SONAR_PROJECT_KEY = 'qjs'
        SONAR_PROJECT_NAME = 'qjs'

        SONAR_TOKEN = credentials('SONAR_TOKEN')
    }

    stages {

        stage('Checkout') {
            steps {
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

        stage('SonarQube Analysis') {

            steps {

                withSonarQubeEnv('sonarqube-server') {

                    sh 'apk add --no-cache curl unzip openjdk17-jre'

                    sh '''
                        curl -sSLo /tmp/sonar-scanner.zip \
                        https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-6.2.1.4610.zip
                    '''

                    sh 'unzip -q /tmp/sonar-scanner.zip -d /opt'

                    sh '''
                        /opt/sonar-scanner-6.2.1.4610/bin/sonar-scanner \
                        -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                        -Dsonar.projectName=${SONAR_PROJECT_NAME} \
                        -Dsonar.sources=src \
                        -Dsonar.host.url=${SONAR_HOST_URL} \
                        -Dsonar.token=${SONAR_TOKEN} \
                        -Dsonar.exclusions=node_modules/**,dist/**
                    '''
                }
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
    }

    post {

        success {
            echo 'SonarQube Analysis Successful'
        }

        failure {
            echo 'Pipeline Failed'
        }
    }
}
