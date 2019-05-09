#!/usr/bin/env groovy

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

def mvn = "${tool 'mvn-default'}/bin/mvn"

node {
    echo "current branch is ${env.BRANCH_NAME}"

    checkout scm

    stage('Build') {
        echo 'Building...'

        sh "${mvn} clean install -Dskiptest"
    }

    if (currentBuild.result == null || currentBuild.result == true) {
        if (params.autoDeploy == true) {
            try {}
            timeout(time: 1, unit: 'HOURS') {
                env.DEPLOY_ENV = input messages: 'User input required',
                        ok: 'Go',
                        parameters: [
                                choice(name: 'Branch to deploy', choices: 'dev1\ndev2', description: 'What Beanstalk environment you want deploy to?')
                        ]
            }

            stage
        }
    }
}
