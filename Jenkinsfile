pipeline {
    agent any

    stages {

        stage('install  os deps') {
            steps {
                sh 'apt-get update && apt-get install npm -y'
            }
        }

        stage('install node deps') {
            steps {
                sh 'npm install'
            }
        }

        stage("Install Test Report"){

            steps{
                sh 'npm install --save-dev jest-junit'
            }
        }
    }
}