pipeline {
    agent any
    properties([parameters([[$class: 'ChoiceParameter', choiceType: 'PT_SINGLE_SELECT', description: 'please select the environment', filterLength: 1, filterable: false, name: 'Environment', randomName: 'choice-parameter-1254271679168869', script: [$class: 'GroovyScript', fallbackScript: [classpath: [], sandbox: true, script: 'return[\'error\']'], script: [classpath: [], sandbox: true, script: '''return[
"\\n",
\'Dev\',
\'Int\',
\'Stag\'
]
''']]]])])


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
