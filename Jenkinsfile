pipeline {
	agent any

	options {
		buildDiscarder(logRotator(numToKeepStr:'5'))
	}


	parameters {
		activeChoiceParam('environment'){
                        description('select your environment')
			choiceType('PT_SINGLE_SELECT')
			groovyscript {
				script("return['Dev','Init','Prd']")
				fallbackScript('return["error"]')
			}
		}
		
		activeChoiceParam ('version'){
			description('select the version')
			choiceType('PT_SINGLE_SELECT')
			
			groovyscript {
				script {
					if (environment.equals("Dev")){return['current version'] } 
					else if(environment.equals("Init")){return['current version', 'Promote from Dev'] } 
					else if(environment.equals("Prd")){return['current version', 'Promote from Init'] } 
					else {return ['unknown'] }
					} 
				fallbackScript('return["error"]')
			}
			referencedParameter('Environment')
		
	}
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
