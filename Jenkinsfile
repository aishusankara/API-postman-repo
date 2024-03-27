
pipeline  {
    tools {
        nodeJS 'NODEJS20.9.0'
        }

    agent {
        label 'general'
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
                expression { $params.PL == 'Kappa' }
                 }
            steps {
                                  println "kappa choice made"
                  }
                    }
    
        
        stage("Run in Staging"){
                when{
                    expression { $params.PL == 'Staging'} 
                }
                steps{
                    
                                 println "staging choice made"
                }
            }
     }
     post {
        always{
            script{
                //do nothing 
                println "Cleaning ok"
                //deleteDir()
                }
            }
        }
 }


