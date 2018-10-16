pipeline {
	agent any

	options {
		buildDiscarder(logRotator(numToKeepStr:'5'))
	}


def environment
 
def version

    
			
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
       
	stage ('build'){
        environment = input( id: 'userInput', message: 'Select your choice', parameters: [ [choices: 'Dev\nInt\nPrd', description: 'Select your environment', name: 'environment'] ])
        if(environment.equals("Dev")){
            version = input( id: 'userInput', message: 'Select your choice', parameters: [ [choices: 'current', description: 'current version build only', name: 'J'] ])
        }else if(environment.equals("Int"){
            version = input( id: 'userInput', message: 'Select your choice', parameters: [ [choices: 'current\npromote from Dev', description: 'choose the requiredversion', name: 'S'] ])
        }
        else if(environment.equals("Prd"){
            version = input( id: 'userInput', message: 'Select your choice', parameters: [ [choices: 'current\npromote from Int', description: 'choose the requiredversion', name: 'W'] ])
        }
        else{
            version = input( id: 'userInput', message: 'Select your choice', parameters: [ [choices: 'unknown', description: 'no version selected', name: 'X'] ])
        }
    }
}

    
}	
