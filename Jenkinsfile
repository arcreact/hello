pipeline {
	agent any

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

    
}	
