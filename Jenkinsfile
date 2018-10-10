pipeline {
    agent any
    options {
    buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '15')), 
parameters([[$class: 'ChoiceParameter', choiceType: 'PT_SINGLE_SELECT', description: 'Please select the environment', filterLength: 1, filterable: false, name: 'Environment', randomName: 'choice-parameter-1289510349829711', script: [$class: 'GroovyScript', fallbackScript: [classpath: [], sandbox: true, script: 'return[\'error\']'], 
script: [classpath: [], sandbox: true, script: '''return[
\'\',
\'Dev\',
\'Int\',
\'Stag\'
]
'''
]]],
 [$class: 'CascadeChoiceParameter', choiceType: 'PT_SINGLE_SELECT', description: 'Please select the version', filterLength: 1, filterable: false, name: 'Version', randomName: 'choice-parameter-1289510362599492', 
 referencedParameters: 'Environment', script: [$class: 'GroovyScript', fallbackScript: [classpath: [], sandbox: true, script: 'return [\'Script Error\']'], 
 script: [classpath: [], sandbox: true, 
 script: '''if (Environment.equals("Dev")){
return["current version"]
} else if(Environment.equals("Int")){
return["current version", "Promote from Dev"]
}else if (Environment.equals("Stag")) {
return["current version", "Promote from Int"]
} else {
return ["unknown state"]
}
'''
]]]]
)

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
