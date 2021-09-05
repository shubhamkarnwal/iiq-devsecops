def INPUT_VARIABLE_xyz	//GLOBAL VARIABLES
def INPUT_VARIABLE_ABC
def INPUT_VARIABLE_PQR
pipeline {
	  agent any
	  environment {
		  creds = credentials('<Credential_ID_VALUE>')
		}
	  stages {
		  stage('HOW_TO_TAKE_INPUT_FROM_USER_IN_PIPELINE') {
			 agent none
			 steps {
					emailext body: '''BODY TEXT''', subject: 'INPUT_STRING', to: 'abc@xyz.com;pqr@xyz.com'
					timeout(time: 600, unit: 'SECONDS') {			//WAIT FOR MANUAL APPROVAL
						script {
							def INPUT_VARIABLE = input id: 'Deploy', message: '?????', submitter: 'userid,userid,userid', parameters: [choice(choices: ['OPTION1', 'OPTION2'], description: '????', name: 'INPUT_VARIABLE_xyz')]
							INPUT_VARIABLE_xyz=INPUT_VARIABLE
							INPUT_VARIABLE_ABC="XXX"
							INPUT_VARIABLE_PQR="XXX"
						}
					}
				}
			}		  
		  stage('HOW_TO_ADD_STAGES_WITHIN_A_STAGE') {
			  stages('PARENT_STAGE_WITH_POST_ACTION') {
				  stage('HOW_TO_TRIGGER_ANOTHER_PIPELINE_WITHOUT_WAITING') {
                      steps {                        
                        build job: 'ABC-JOB', parameters: [string(name: 'INPUT_VARIABLE_xyz', value: "${INPUT_VARIABLE_xyz}"), string(name: 'INPUT_VARIABLE_PQR', value: "${INPUT_VARIABLE_PQR}")], wait: false
                      }											
				  }
			  	}
				post {
					failure {																				
						build job: 'JOB_TO_BE_TRIGGERED_ON_FAILURE_OF_ABOVE_STAGES', parameters: [[$class: 'StringParameterValue', name: 'INPUT_VARIABLE_ABC', value: "$INPUT_VARIABLE_ABC"]]								
					}
				}			  
			}			
		  stage('UCD_STAGE') {	
			  steps {		
					withCredentials([usernamePassword(credentialsId: 'ucd-creds', passwordVariable: 'PassToken', usernameVariable: 'Uname')]) {
					   step([$class: 'UCDeployPublisher',
							siteName: 'ucd-devops.kp.org',
							altUser: [
								altPassword: hudson.util.Secret.fromString(PassToken),
								altUsername: "$UNAME"
							],		
							component: [
								$class: 'com.urbancode.jenkins.plugins.ucdeploy.VersionHelper$VersionBlock',
								componentName: 'NAME_OF_THE_COMPONENT_IN_UCD_WHERE_VERSION_WILL_BE_PUSHED',
								createComponent: [
									$class: 'com.urbancode.jenkins.plugins.ucdeploy.ComponentHelper$CreateComponentBlock',
									componentTemplate: '',
									componentApplication: 'NAME_OF_APPLICATION_IN_UCD_WHICH_IS_CONNECTED_WITH_COMPONENT'
								],
								delivery: [
									$class: 'com.urbancode.jenkins.plugins.ucdeploy.DeliveryHelper$Push',
									pushVersion: '${BUILD_NUMBER}',
									baseDir: "${WORKSPACE}",	//BUILD FILES LOCATION
									fileIncludePatterns: '**/*',
									fileExcludePatterns: '',
									pushProperties: 'jenkins.server=Local\njenkins.reviewed=false\nINPUT_VARIABLE_xyz=${INPUT_VARIABLE_xyz}',
									pushDescription: 'Pushed from Jenkins',
									pushIncremental: false
								]
							]
						])
					   step([$class: 'UCDeployPublisher',
							siteName: 'ucd-devops.kp.org',
							deploy: [
								$class: 'com.urbancode.jenkins.plugins.ucdeploy.DeployHelper$DeployBlock',
								deployApp: 'NAME_OF_APPLICATION_IN_UCD_WHICH_IS_CONNECTED_WITH_COMPONENT',
								deployEnv: "${INPUT_VARIABLE_ABC}",
								deployProc: 'NAME_OF_APPLICATION_PROCESS_IN_UCD_WHICH_IS_CONNECTED_WITH_COMPONENT',
								createProcess: [
									$class: 'com.urbancode.jenkins.plugins.ucdeploy.ProcessHelper$CreateProcessBlock',
									processComponent: 'Deploy'
								],
								deployVersions: '${BUILD_NUMBER}',
								deployReqProps: "INPUT_VARIABLE_xyz=${INPUT_VARIABLE_xyz}",
								deployOnlyChanged: true
							]
						])
					}
				}
			}
		}
		post {
			success {				
				build job: 'ANOTHER_JOBBB', parameters: [[$class: 'StringParameterValue', name: 'INPUT_VARIABLE_ABC', value: "$INPUT_VARIABLE_ABC"]], wait: false
			}
		}		
	}
