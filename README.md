# Automated Testing Pipeline with Jenkins ( Master & Slave confiugration)

This repository contains a Jenkins pipeline script for executing automated tests using WebDriverIO and BrowserStack Automate.The pipeline script will split features file equally across number of slaves machine in runtime

## Prerequisites

Make sure you have the following prerequisites installed:

- [Jenkins](https://jenkins.io/)
- [Node.js](https://nodejs.org/) (for npm)
- [WebDriverIO](https://webdriver.io/)
- [BrowserStack Account](https://www.browserstack.com/)

## Setup

1. **Clone this repository:**

    ```bash
    git clone https://github.com/sanketsmali03/wdio_bstack_automate_sample.git
    cd wdio_bstack_automate_sample
    ```

2. **Set up your Jenkins environment variables:**

    - `BROWSERSTACK_USERNAME`: Your BrowserStack username
    - `BROWSERSTACK_ACCESS_KEY`: Your BrowserStack access key

## Jenkins Pipeline Configuration

1. **Create a Jenkins pipeline job and use the following script in your Jenkinsfile:**

    ```groovy
    pipeline {
        agent none

        environment {
            BROWSERSTACK_BUILD_NAME = "${currentBuild.startTimeInMillis}"
        }

        stages {
            stage('Run Tests') {
                steps {
                    script {
                        // Define your feature files
                        def featureFiles = [
                            './features/login1.feature',
                            './features/login2.feature',
                            './features/login3.feature',
                            // Add more feature files as needed
                        ]

                        // Define the number of slave workers
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

                                    // Clone the repository
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
    ```

2. **Update the `featureFiles` array with your actual feature file paths.**

3. **Trigger the Jenkins pipeline job to execute the tests.**

Feel free to add more details or customize the instructions based on your specific setup and requirements!
