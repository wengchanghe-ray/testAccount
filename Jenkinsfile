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

node {
   checkout scm
   def autoDeploy = params.autoDeploy

   echo "${env.BRANCH_NAME}"
   echo "${autoDeploy}"

   if(autoDeploy) {
      echo "To deploy!"
   }
}
