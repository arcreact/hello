pipeline {
	agent {label linuxAgent}

	options {
		buildDiscarder(logRotator(numToKeepStr:'5'))
	}


	parameters {
		choice(
			name: 'Environment',
			choices: "Dev\nInit\nPrd",
			description: 'select the environment' )
	}
			
stages{
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
}

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
}	
