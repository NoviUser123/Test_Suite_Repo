pipeline {
    agent any

    environment {
        MAJOR                   = '1'
        MINOR                   = '0'
        PATH                    = "C:\\Program Files\\dotnet;${env.PATH}"
        UIPATH_ORCH_URL         = "https://cloud.uipath.com/noviorg/DefaultTenant/orchestrator_/"
        UIPATH_ORCH_LOGICAL_NAME = "noviorg"
        UIPATH_ORCH_TENANT_NAME = "DefaultTenant"
        UIPATH_ORCH_FOLDER_NAME = "TestSuite"
    }

    stages {
        stage('Preparing') {
            steps {
                echo "Jenkins Home ${env.JENKINS_HOME}"
                echo "Jenkins URL ${env.JENKINS_URL}"
                echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
                echo "Jenkins JOB Name ${env.JOB_NAME}"
                echo "GitHub BranchName ${env.BRANCH_NAME}"
                checkout scm
            }
        }

        stage('Verify dotnet') {
            steps {
                bat 'echo %PATH%'
                bat 'where dotnet'
                bat 'dotnet --version'
            }
        }

        stage('Build') {
            steps {
                echo "Building with ${WORKSPACE}"
                UiPathPack(
                    outputPath:       "Output\\${env.BUILD_NUMBER}",
                    projectJsonPath:  "project.json",
                    version:          [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
                    useOrchestrator:  false,
                    traceLevel:       'None'
                )
            }
        }

        stage('Test') {
            steps {
                echo 'Testing the workflow...'
            }
        }

        stage('Deploy to UAT') {
            steps {
                echo "Deploying ${env.BRANCH_NAME} to UAT"
                UiPathDeploy(
                    packagePath:        "Output\\${env.BUILD_NUMBER}",
                    orchestratorAddress: "${UIPATH_ORCH_URL}",
                    orchestratorTenant:  "${UIPATH_ORCH_TENANT_NAME}",
                    folderName:          "${UIPATH_ORCH_FOLDER_NAME}",
                    environments:        '',
                    // use PAT via Secret Text
                    credentials:         [ $class: 'RefreshTokenAuthenticationEntry', credentialsId: 'APIUserKey' ],
                    traceLevel:          'None',
                    entryPointPaths:     'Main.xaml',
                    createProcess:       true
                )
            }
        }

        stage('Deploy to Production') {
            steps {
                echo 'Deploy to Production'
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
