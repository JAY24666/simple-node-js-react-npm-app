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

        stage("Build"){

            steps{
                sh 'npm run build'
            }
        }

        stage('Run Tests'){

            steps{
                sh 'npm test -- --ci --reporters=default --reporters=jest-junit'
            }
        }
    }
}