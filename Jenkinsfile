pipeline {
	agent any

	options {
		buildDiscarder(logRotator(numToKeepStr:'5'))
	}


	parameters {
		activeChoiceParam('Environment'){
                        description('select your environment')
			choiceType('PT_SINGLE_SELECT')
			groovyscript {
				script("return['DEV' , 'Init' ,'Prd']")
				fallbackScript('return["error"]')
			}
		}
		
		activeChoiceParam ('version'){
			description('select the version')
			choiceType('PT_SINGLE_SELECT')
			
			groovyscript {
				script ("if (Environment.equals("DEV")){return['current version'] } else if(Environment.equals("Init")){return['current version', 'Promote from Dev'] } else if(Environment.equals("Prd"){return['current version', 'promote from Init'] }") 
				fallbackScript('return["error"]')
			}
			referencedParameter('Environmet')
		
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
