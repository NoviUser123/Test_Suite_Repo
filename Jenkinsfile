pipeline {
    agent any

    environment {
        MAJOR = '1'
        MINOR = '0'

        // Orchestrator Services
        UIPATH_ORCH_URL = "https://cloud.uipath.com/"
        UIPATH_ORCH_LOGICAL_NAME = "persorrxarsb" // your org logical name
        UIPATH_ORCH_TENANT_NAME = "DefaultTenant"
        UIPATH_ORCH_FOLDER_NAME = "TestSuite"
    }

    stages {

        // Preparing environment
        stage('Preparing') {
            steps {
                echo "Jenkins Home: ${env.JENKINS_HOME}"
                echo "Jenkins URL: ${env.JENKINS_URL}"
                echo "Jenkins Job Number: ${env.BUILD_NUMBER}"
                echo "Jenkins Job Name: ${env.JOB_NAME}"
                echo "GitHub Branch Name: ${env.BRANCH_NAME}"
                checkout scm
            }
        }

        // Build Package using External App Auth
        stage('Build Package') {
            steps {
                echo "Start building the package on ${WORKSPACE}\\Output"
                UiPathPack(
                    credentials: ExternalApp(
                        accountForApp: "${UIPATH_ORCH_LOGICAL_NAME}",
                        applicationId: '42778989-c006-41c7-a13f-3f4651b99034',
                        applicationScope: 'OR.Assets OR.BackgroundTasks OR.Execution OR.Folders OR.Jobs OR.Machines.Read OR.Monitoring OR.Robots.Read OR.Settings.Read OR.TestSetExecutions OR.TestSets OR.TestSetSchedules OR.Users.Read',
                        applicationSecret: 'ApplicationSecret',
                        identityUrl: ''
                    ),
                    orchestratorAddress: "${UIPATH_ORCH_URL}",
                    orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
                    outputPath: "${WORKSPACE}\\Output",
                    outputType: 'Process',
                    projectJsonPath: 'C:\\Users\\sanjay.bhat\\OneDrive - Novigo Solutions Pvt. Ltd\\Documents\\UiPath\\BlankProcess1\\project.json',
                    splitOutput: false,
                    disableBuiltInNugetFeeds: false,
                    traceLevel: 'Verbose',
                    useOrchestrator: true,
                    version: CurrentVersion()
                )
            }
        }

        // Deploy to UAT using External App Auth
        stage('Deploy to UAT') {
            steps {
                echo "Deploying ${BRANCH_NAME} to UAT"
                UiPathDeploy(
                    packagePath: "${WORKSPACE}\\Output",
                    orchestratorAddress: "${UIPATH_ORCH_URL}",
                    orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
                    folderName: "${UIPATH_ORCH_FOLDER_NAME}",
                    environments: '',
                    credentials: ExternalApp(
                        accountForApp: "${UIPATH_ORCH_LOGICAL_NAME}",
                        applicationId: '42778989-c006-41c7-a13f-3f4651b99034',
                        applicationScope: 'OR.Execution OR.Folders OR.Jobs OR.Processes OR.Robots.Read',
                        applicationSecret: 'ApplicationSecret',
                        identityUrl: ''
                    ),
                    traceLevel: 'None',
                    entryPointPaths: 'Main.xaml',
                    createProcess: true
                )
            }
        }

        // Deploy to Production
        stage('Deploy to Production') {
            steps {
                echo "Deploy to Production"
            }
        }
    }

    options {
        timeout(time: 80, unit: 'MINUTES')
        skipDefaultCheckout()
    }

    post {
        success {
            echo 'Deployment has been completed!'
        }
        failure {
            echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
        }
        always {
            cleanWs()
        }
    }
}
