#!/usr/bin/env groovy

import org.jenkinsci.plugins.workflow.steps.FlowInterruptedException

def getAutoDeploy() {
    if (env.BRANCH_NAME == 'develop') {
        booleanParam(defaultValue: true, description: 'Auto deploy when build is successful?', name: 'autoDeploy')
    } else {
        booleanParam(defaultValue: false, description: 'Auto deploy when build is successful?', name: 'autoDeploy')
    }
}

properties([
        parameters([
                getAutoDeploy(),
                string(defaultValue: 'ap-south-1', description: 'AWS Region', name: 'region')
        ]),
        buildDiscarder(logRotator(artifactNumToKeepStr: '30', numToKeepStr: '30')),
        pipelineTriggers([pollSCM('*/5 * * * *')])
])


node {
    echo "current branch is ${env.BRANCH_NAME}"

    checkout scm

    def mvn = "${tool 'mvn-default'}/bin/mvn"

    stage('Build') {
        echo 'Building...'

        sh "${mvn} clean install -Dskiptest"
    }

    if (currentBuild.result == null || currentBuild.result == true) {
        if (params.autoDeploy == true) {
            def shouldDeploy = true
            try {

                timeout(time: 1, unit: 'HOURS') {
                    env.DEPLOY_ENV = input messages: 'User input required',
                            ok: 'Go',
                            parameters: [
                                    choice(name: 'Branch to deploy', choices: 'dev1\ndev2', description: 'What Beanstalk environment you want deploy to?')
                            ]
                }
            }
            catch (FlowInterruptedException ex) {
                echo "Flow interrupted ${ex}"
                currentBuild.result = "ABORTED"
                shouldDeploy = false

                echo "Can process here after ABORTED?"
            }

            echo "Move on1?"

            if(shouldDeploy) {
                stage('Deploy') {
                    echo "Deploying..."

                }
            }
        }
    }
}
