pipeline {
	    agent any
	

	        // Environment Variables
	        environment {
	        MAJOR = '1'
	        MINOR = '0'
	        //Orchestrator Services
	           // Orchestrator Services
        UIPATH_ORCH_URL = "https://cloud.uipath.com/"
        UIPATH_ORCH_LOGICAL_NAME = "noviorg"
        UIPATH_ORCH_TENANT_NAME = "DefaultTenant"
        UIPATH_ORCH_FOLDER_NAME = "TestSuite"
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
	       stage ('Build Package') {
        steps {
            echo "Start building the package on ${WORKSPACE}\\Output"
            UiPathPack(
            credentials: ExternalApp(accountForApp: 'persorrxarsb',
            applicationId: '42778989-c006-41c7-a13f-3f4651b99034', 
            applicationScope: 'OR.Assets OR.BackgroundTasks OR.Execution OR.Folders OR.Jobs OR.Machines.Read OR.Monitoring OR.Robots.Read OR.Settings.Read OR.TestSetExecutions OR.TestSets OR.TestSetSchedules OR.Users.Read', 
            applicationSecret: 'CU#JLLS)Jo%!*bGemeWM(4DkYegu_vY_nfnwvwJj7DU12EIJt1Bl6f%OGB@(_AzE', 
            identityUrl: ''), 
            orchestratorAddress: 'https://cloud.uipath.com/',
             orchestratorTenant: 'DefaultTenant', 
             outputPath: '${WORKSPACE}\\Output',
             outputType: 'Process',
             projectJsonPath: '"C:\\Users\\sanjay.bhat\\OneDrive - Novigo Solutions Pvt. Ltd\\Documents\\UiPath\\BlankProcess1\\project.json"', 
             projectUrl: '',
             repositoryBranch: '', 
             repositoryCommit: '',
             repositoryType: '', 
             repositoryUrl: '', 
             splitOutput: false,
             disableBuiltInNugetFeeds: false,
             traceLevel: 'Verbose',
             useOrchestrator: true,
             version: CurrentVersion())
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
