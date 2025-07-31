pipeline {
    agent any

    // ✅ Environment Variables
    environment {
        MAJOR = '1'
        MINOR = '0'

        // ✅ Add dotnet to PATH
        PATH = "C:\\Program Files\\dotnet;${env.PATH}"

        // Orchestrator Services
        UIPATH_ORCH_URL = "https://cloud.uipath.com/noviorg/DefaultTenant/orchestrator_/"
        UIPATH_ORCH_LOGICAL_NAME = "noviorg"
        UIPATH_ORCH_TENANT_NAME = "DefaultTenant"
        UIPATH_ORCH_FOLDER_NAME = "TestSuite"
    }

    stages {

        // 📌 Preparing Stage
        stage('Preparing') {
            steps {
                echo "Jenkins Home ${env.JENKINS_HOME}"
                echo "Jenkins URL ${env.JENKINS_URL}"
                echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
                echo "Jenkins JOB Name ${env.JOB_NAME}"
                echo "GitHub BranhName ${env.BRANCH_NAME}"
                checkout scm
            }
        }

        // 🔍 Debug dotnet availability
        stage('Verify dotnet') {
            steps {
                bat 'echo %PATH%'
                bat 'where dotnet'
                bat 'dotnet --version'
            }
        }

        // 🛠 Build Stage
        stage('Build') {
            steps {
                echo "Building..with ${WORKSPACE}"
                UiPathPack(
                    outputPath: "Output\\${env.BUILD_NUMBER}",
                    projectJsonPath: "project.json",
                    version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
                    useOrchestrator: false,
                    traceLevel: 'None'
                )
            }
        }

        // ✅ Test Stage
        stage('Test') {
            steps {
                echo 'Testing..the workflow...'
            }
        }

        // 🚀 Deploy to UAT Stage
        stage('Deploy to UAT') {
            steps {
                echo "Deploying ${BRANCH_NAME} to UAT "
                UiPathDeploy(
                    packagePath: "Output\\${env.BUILD_NUMBER}",
                    orchestratorAddress: "${UIPATH_ORCH_URL}",
                    orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
                    folderName: "${UIPATH_ORCH_FOLDER_NAME}",
                    environments: '',
                    credentials:  credentialsId: 'APIUserKey',
                    traceLevel: 'None',
                    entryPointPaths: 'Main.xaml',
                    createProcess: true
                )
            }
        }

        // 🚀 Deploy to Production Stage
        stage('Deploy to Production') {
            steps {
                echo 'Deploy to Production'
            }
        }
    }

    // 🕒 Options
    options {
        timeout(time: 80, unit: 'MINUTES')
        skipDefaultCheckout()
    }

    // ✅ Post Actions
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