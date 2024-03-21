@Library('CleanUp') _ 
pipeline { 
    agent any 
    stages { 
        stage('Sending Email Before CleanUp')
        {
            steps {
                script {
                    sendEmailNotificationbeforecleanup()
                }
            }
        }
        stage('Workspace Cleanup') {
            steps {
                script {
                    def cleanupResult = workspaceCleanup()
                    if (cleanupResult == 'SUCCESS') {
                        echo 'Skipping Build stage'
                        currentBuild.result = 'SUCCESS'
                        return
                    }
                }
            }
        }
        stage('Sending Email after CleanUp')
        {
            steps {
                script {
                    sendEmailNotificationaftercleanup()
                }
            }
        }
         stage('Build') {
            steps {
                script {
                        if(currentBuild.result != 'SUCCESS')
                        {
                        sh './gradlew build' 
                        }
                        else
                        {
                            echo 'skipping Build'
                        }
                    }
                }
        }
        stage('Test') {
            steps {
                script {
                        if(currentBuild.result != 'SUCCESS'){
                        // Run your unit tests here
                            junit(testResults: 'build/test-results/test/*.xml', allowEmptyResults: true, skipPublishingChecks: true)
                            def testResult = sh(returnStatus: true, script: './gradlew test')
                                if (testResult != 0) {
                                    error('Unit tests failed. Failing the build.')
                                }
                        }
                        else{
                            echo 'skipping stage' 
                        }
                }
            }
        }
    }
} 
