pipeline {
    agent any
 environment {
        NEXUS_URL = 'http://3.80.124.151:8081'
        NEXUS_REPO = 'npm-releases'
        CREDENTIALS_ID = 'nexus-creds'
    }
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
        
        stage('Publish Test Results'){
            steps{
                junit 'junit.xml'
            }
        }

         stage("Run Code Analysis"){
            environment {
                SCANNER_HOME = tool 'SonarScanner'
            }
            steps {

                withSonarQubeEnv('Sonarserver') {
                   sh '''$SCANNER_HOME/bin/sonar-scanner \
                       -Dsonar.projectKey=MyNpm \
                       -Dsonar.projectName=NpmProject \
                       -Dsonar.sources=. \
                       -Dsonar.analysis.mode=publish \
                       -Dsonar.projectVersion=${BUILD_NUMBER}-${GIT_COMMIT_SHORT}
                    
                    '''
                }
            }
        }
        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }

        }
        stage('Upload to Nexus') 
        {
            steps {
                script {
                    def packageFile = sh(
                        script: "ls *.tgz",
                        returnStdout: true
                    ).trim()

                    withCredentials([usernamePassword(credentialsId: "${env.CREDENTIALS_ID}", usernameVariable: 'admin', passwordVariable: 'Administrator@123')]) {
                        sh """
                        curl -v --user $USERNAME:$PASSWORD \
                            --upload-file ${packageFile} \
                            ${NEXUS_URL}/repository/${NEXUS_REPO}/${packageFile}
                        """
                    }
                }
            }
        }
    }
}

