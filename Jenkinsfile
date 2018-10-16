pipeline {
	agent any

	options {
		buildDiscarder(logRotator(numToKeepStr:'5'))
	}


	parameters {
		choice(
			name: 'Environment',
			choices: "\nDev\nInit\nPrd",
			description: 'select the environment' )
		activeChoiceReactiveReferenceParam(
			name: 'version',
			description: 'selct the version',
			choiceType: 'PT_SINGLE_SELECT',
			referencedParameter: 'Environment',
			groovyscript :
				script {
					if (Environment.equals("Dev")){
						return["current version"] }
						       else if(Environment.equals("Init")){
							       return["current version", "Promote from Dev"] }
						       else if(Environment.equals("Prd"){
							       return["current version","promote from Init"] } }
							       )			 					       
				
		 
			
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
