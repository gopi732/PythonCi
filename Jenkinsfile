// Declarative pipeline
pipeline {
    agent any
    stages {
        stage ('Clean Reports') {
            steps {
			    echo '********* Cleaning Workspace Stage Started **********'
                sh 'rm -rf /s /q test-reports || true'  
			    echo '********* Cleaning Workspace Stage Finished **********'
            }
        }
		stage ('test') {
	        steps {
				echo '********* Test Stage Started **********'
				sh 'python3 test.py'
				echo '********* Test Stage Finished **********'
			}
		}
		
		stage('SonarQube Analysis') {
			environment {
				scannerHome = tool 'SonarQube Scanner'
    			}
			steps {
				echo '********* Test Stage Started **********'
				
				withSonarQubeEnv('Sonar') {
					sh '${scannerHome}/bin/sonar-scanner \
					-D sonar.projectKey=python'
				}
				echo '********* Test Stage Finished **********'
			}
		}
        stage ('Generate Test Reports') {
            steps {
		echo '******** Generating Test Reports *********'    
                junit 'test-reports/*.xml' 
		echo '******** Generating Test Reports *********'  
            }
        }
		stage ('Publish Artifactory') {
		    steps {
				echo '********* Publish Report to JFrog Artifacts **********' 
				withCredentials([usernamePassword(credentialsId: 'artifactory', passwordVariable: 'passwd', usernameVariable: 'user')]) {
					sh 'jf rt upload test-reports/ python-app/'
				}
				echo '********* Publish Report Finished **********'	
			}
		}
	}
}
