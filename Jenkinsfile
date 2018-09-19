pipeline {
    agent any
    parameters {
        string(name: 'VERSION', defaultValue: '')
    }   
    environment {          
        def VERSION = "${params.VERSION}"
    }
    stages {
        stage('Prepare & Checkout') {
            steps {
                script {
                    if (! env.VERSION) {
                       VERSION = sh(script: "date", returnStdout: true).trim()
                    }
                    echo "** version: ${VERSION} **"
                }
            }
        }  
        stage('Build') {
            steps {
                // sh "./build.sh"
                echo "** version2: ${VERSION} **"
            }
        }
    } // stages
    post {
        always {
            mail to: 'foo@example.com',
                 subject: "SUCCESS: ${VERSION}",
                 body: """<html><body><p>SUCCESS</p></body></html>""",
                 mimeType: 'text/html',
                 charset: 'UTF-8'
            deleteDir()
        }
    }
} // pipeline
