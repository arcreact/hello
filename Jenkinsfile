def fileVersionParameter(){
	return [$class: 'CascadeChoiceParameter', choiceType: 'PT_SINGLE_SELECT', description: '', filterLength: 1, filterable: false, name: 'fileVersion', randomName: 'choice-parameter-3460752251980003', referencedParameters: 'environment,stack', 
			script: [$class: 'GroovyScript', 
			fallbackScript: [classpath: [], sandbox: true, script: 'return [\'Something wrong in the groovy script\']'], 
			script: [classpath: [], sandbox: true, script: '''
				if(environment.equals(\'Dev\')){
					return [\'Current\']
				} else if(environment.equals(\'Sit\')){					
					return [\'Current\']
				} else if(environment.equals(\'Int\')){	
					if(stack.contains(\'Symphony\')){
						return [\'Current\',\'Previous\',\'Promote from Sit\']
					}
					else {
						return [\'Current\']
					}				
					return [\'Current\']
				}  else if(environment.equals(\'Uat\')){
					return [\'Current\',\'Previous\',\'Promote from Int\']
				} else if(environment.equals(\'Pre-prd\')){					
					return [\'Current\',\'Previous\',\'Promote from Uat\']
				} else if(environment.equals(\'Prd\')){
					if(stack.contains(\'Symphony\')){
						return [\'Current\',\'Previous\',\'Promote from Pre-prd\']
					}
					else {			
						return [\'Current\',\'Previous\',\'Promote from Uat\']
					}
				} else{
					return [\'Current\',\'Previous\']
				}
'''
			]]]
}
pipeline {
    agent any
    

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
