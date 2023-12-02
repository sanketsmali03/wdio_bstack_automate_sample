pipeline {
    agent none

    stages {
        stage('Run Tests') {
            steps {
                script {
                    def featureFiles = [
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


                    def numAgents = 2
                    def filesPerAgent = featureFiles.size() / numAgents

                    def agentFeatureMap = [
                        "Slave1": featureFiles[0..(filesPerAgent - 1)],
                        "Slave2": featureFiles[filesPerAgent..-1]
                    ]

                    parallel agentFeatureMap.collectEntries { agentName, files ->
                        ["Run on ${agentName}" : {
                            node(agentName) {                                
                                echo "Running tests on ${agentName}: ${files}"
                                echo "Current directory: ${pwd()}"
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

                    browserStackReportPublisher 'automate'
                }
            }
        }
    }
}
