pipeline {

    agent {
        docker {
            image 'node:20-alpine'
            args '-u root'
        }
    }

    environment {

        SONAR_HOST_URL = 'http://192.168.56.1:9000'

        SONAR_PROJECT_KEY = 'qjs'
        SONAR_PROJECT_NAME = 'qjs'

        SONAR_TOKEN = credentials('SONAR_TOKEN')
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub...'
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
                        https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-5.0.1.3006-linux.zip

                        unzip -q /tmp/sonar-scanner.zip -d /opt
                    '''

                    sh '''
                        /opt/sonar-scanner-5.0.1.3006-linux/bin/sonar-scanner \
                        -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                        -Dsonar.projectName="${SONAR_PROJECT_NAME}" \
                        -Dsonar.sources=src \
                        -Dsonar.host.url=${SONAR_HOST_URL} \
                        -Dsonar.login=${SONAR_TOKEN} \
                        -Dsonar.exclusions=node_modules/**,dist/** \
                        -Dsonar.typescript.tsconfigPath=tsconfig.json
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

        always {
            echo 'Pipeline completed!'
        }

        success {
            echo 'Build successful! SonarQube analysis completed successfully.'
        }

        failure {
            echo 'Build failed. Check Jenkins console logs and SonarQube.'
        }
    }
}
