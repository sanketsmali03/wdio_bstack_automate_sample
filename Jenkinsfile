pipeline {
    agent none

    environment {

        // Setting Build Name varialbe in runtime
        BROWSERSTACK_BUILD_NAME = "${currentBuild.startTimeInMillis}"
        }
    stages {
        stage('Run Tests') {
            steps {
                script {
                    def featureFiles = [

                        //List feature files 
                        './features/login1.feature',
                        './features/login2.feature',
                        './features/login3.feature',
                        './features/login4.feature',
                        './features/login5.feature',
                        './features/login6.feature',
                        './features/login7.feature',
                        './features/login8.feature',
                        './features/login9.feature',
                        './features/login10.feature'

                    ]

                    // Define Total Slave Workers 
                    def numAgents = 2
                    def filesPerAgent = featureFiles.size() / numAgents

                    def agentFeatureMap = [
                        // Split the feature files across different Slaves
                        "Slave1": featureFiles[0..(filesPerAgent - 1)],
                        "Slave2": featureFiles[filesPerAgent..-1]
                    ]

                    parallel agentFeatureMap.collectEntries { agentName, files ->
                        ["Run on ${agentName}" : {
                            node(agentName) {                     

                                // Which features files runs on which slave
                                echo "Running tests on ${agentName}: ${files}"
                                echo "Current directory: ${pwd()}"

                                // Clone for git
                                git branch: 'main', url: 'https://github.com/sanketsmali03/wdio_bstack_automate_sample.git'
                                withEnv([
                                    'BROWSERSTACK_USERNAME=' + env.BROWSERSTACK_USERNAME,
                                    'BROWSERSTACK_ACCESS_KEY=' + env.BROWSERSTACK_ACCESS_KEY
                                ]) {
                                    sh "npm install"
                                    sh "npx wdio wdio.conf.js --spec ${files.join(' ')}"
                                    
                                }
                            }
                        }]
                    }
                }
            }
        }
    }
}
