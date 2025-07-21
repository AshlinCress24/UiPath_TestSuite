pipeline {
	    agent any

	   environment {
                   UIPATH_CRED = credentials('APIUserKey') // resolves to Secret Text
           }
	

	    stages {
	

	        // Printing Basic Information
	        stage('Preparing'){
	            steps {
	                echo "Jenkins Home ${env.JENKINS_HOME}"
	                echo "Jenkins URL ${env.JENKINS_URL}"
	                echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
	                echo "Jenkins JOB Name ${env.JOB_NAME}"
	                echo "GitHub BranhName ${env.BRANCH_NAME}"
	                checkout scm
	

	            }
	        }
	

	         // Build Stages
	        stage('Build') {
	            steps {
	                echo "Building..with ${WORKSPACE}"
	                UiPathPack (
	                      outputPath: "Output\\${env.BUILD_NUMBER}",
	                      projectJsonPath: "project.json",
	                      version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
	                      useOrchestrator: false,
			      traceLevel: 'None'
	        )
	            }
	        }
	         // Test Stages
	        stage('Test') {
	            steps {
	                echo 'Testing..the workflow...'
	            }
	        }
	

	         // Deploy Stages
	         stage('Deploy to UAT') {
            steps {
                echo "Deploying ${BRANCH_NAME} to UAT"
		echo "Orchestrator URL: ${env.UIPATH_ORCH_URL}"
		echo "Logical Name: ${env.UIPATH_ORCH_LOGICAL_NAME}"
		echo "Tenant: ${env.UIPATH_ORCH_TENANT_NAME}"
		echo "Folder: ${env.UIPATH_ORCH_FOLDER_NAME}"
		withCredentials([string(credentialsId: 'APIUserKey', variable: 'UIPATH_APIKEY')])
                UiPathDeploy (
                    packagePath: "Output\\${env.BUILD_NUMBER}",
                    orchestratorAddress: "${env.UIPATH_ORCH_URL}",
                    orchestratorTenant: "${env.UIPATH_ORCH_TENANT_NAME}",
                    folderName: "${env.UIPATH_ORCH_FOLDER_NAME}",
                    environments: '',
                    createProcess: true,
                    credentials: UIPATH_APIKEY,
                    traceLevel: 'Verbose',
                    entryPointPaths: 'Main.xaml'
                )
            }
        }
	

	

	         // Deploy to Production Step
	        stage('Deploy to Production') {
	            steps {
	                echo 'Deploy to Production'
	                }
	            }
	    }
	

	    // Options
	    options {
	        // Timeout for pipeline
	        timeout(time:80, unit:'MINUTES')
	        skipDefaultCheckout()
	    }
	

	

	    // 
	    post {
	        success {
	            echo 'Deployment has been completed!'
	        }
	        failure {
	          echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
	        }
	        always {
	            /* Clean workspace if success */
	            cleanWs()
	        }
	    }
	

	}
