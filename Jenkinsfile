pipeline {
    // These are pre-build sections.
    agent {
        node {
            label 'AGENT-1'
        }
    }
    environment {
        COURSE = "Jenkins"
        appVersion = ""
        ACC_ID = "930832106480"
        PROJECT = "roboshop"
        COMPONMENT = "catalogue"
    } 
    options {
        timeout(time: 10, unit: 'MINUTES')
        disableConcurrentBuilds()
    }
 // This is build section.
    stages {
        stage('Read Version') {
            steps {
                script{
                    def packageJSON = readJSON file: 'package.json'
                    appVersion = packageJSON.version
                    echo "app version: ${appVersion}"
                }
            }
        }
        stage('Install Dependencies') { 
            steps {
                sh """
                    npm install
                """
            }
        }
        stage('BUILD Image') { 
            steps {
                withAWS(region:'us-east-1',credentials:'aws-creds') {
                    sh """
                    aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com
                    docker build ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONMENT}:${appVersion}
                    docker images
                    docker push ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONMENT}:${appVersion}
                """
                }
            }
        }
    post{
        always{
            echo 'I will always say Hello again!'
            cleanWs()
        }
        success {
            echo 'I will run if sucess'
        }
        failure {
            echo 'I will run if sucess'
        aborted {
            echo 'pipeline is aborted'
        }
        }
    }
}