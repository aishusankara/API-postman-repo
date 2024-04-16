
pipeline  {
    tools {
        nodejs 'NODEJS20.9.0'
        }
  

    agent {
        label 'general'
    }
    
    triggers {
  parameterizedCron ( '''30 2 * * * %PL=Kappa
                         40 2 * * * %PL=Staging''')

        }
    
    parameters {
    choice(
        name: 'PL',
        choices: ['Kappa','Staging'],
        description: 'Choose where to run your code' )
    }
  
    stages {
        stage('Setup') {
            steps {
                script {
                   sh 'node -v'
                   sh 'npm -v'

                   sh 'npm install -g newman'
                   sh 'npm install -g newman-reporter-htmlextra'
                }
            }
        }
        stage('Build') {
            steps {
                  echo " The environment is {$params.PL}"
         
                 
           }
        }
        
        stage ('Run with Kappa') {
            when {
                // Only say hello if a "greeting" is requested
                expression { return params.PL == 'Kappa' }
                 }
            steps {
                                  sh 'npx newman run Decibelendpoints.postman_collection.json -e Kappa.postman_environment.json -r htmlextra'  
                  }
                    }
    
        
        stage("Run in Staging"){
                when{
                    expression { return params.PL == 'Staging'} 
                }
                steps{
                    
                                 sh 'npx newman run Decibelendpoints.postman_collection.json -e Staging.postman_environment.json -r htmlextra'
                }
            }
     }
       post {
		always {
			script {
		cleanWs(cleanWhenNotBuilt: false,
                    deleteDirs: true,
                    disableDeferredWipeout: true,
                    notFailBuild: true,
                    patterns: [[pattern: '.gitignore', type: 'INCLUDE'],
                               [pattern: '.propsfile', type: 'EXCLUDE']])
			}
		}
		
		success {
			sendSlack('SUCCESS')
		}

		failure {
			sendSlack('FAILURE')
		}
	         
	}
}

def sendNotifications(String buildStatus) {
	sendSlack(buildStatus)
}




def sendSlack(String buildStatus) {
	def title = buildStatus != "SUCCESS" ? ":alert-blue: API Endpoints have failed scenarios" : ":check-approved: API Endpoints ran successfully"
	def color = buildStatus != "SUCCESS" ? "FF0000" : "00FF00"

	slackSend channel: 'api-scripts',
                          color: color,
                          message: "${title}\nCheck console output at <${env.BUILD_URL}|${env.JOB_NAME} [${env.BUILD_NUMBER}]>"
}
