#!/usr/bin/env groovy
import org.jenkinsci.plugins.workflow.steps.FlowInterruptedException
def option1 = 'realtime'
def option2 = 'batch'
def option3 = 'both'

def getAutoDeploy() {
    if (env.BRANCH_NAME == 'develop') {
        booleanParam(defaultValue: true, description: '111Auto deploy when build is successful?', name: 'autoDeploy')
    } else {
        booleanParam(defaultValue: false, description: '222Auto deploy when build is successful?', name: 'autoDeploy')
    }
}

properties([
    parameters([
        getAutoDeploy(),
        string(defaultValue: '3333ap-south-1', description: 'AWS Region', name: 'region')
    ]),
    buildDiscarder(logRotator(artifactNumToKeepStr: '30', numToKeepStr: '30')),
    pipelineTriggers([pollSCM('*/5 * * * *')])
])

node {
    echo "${env.BRANCH_NAME}"
    
    checkout scm

    stage('Clean') {
        echo 'Cleanning....'
    }
    
    stage('Test') {
        echo 'Testing....'
    }
    
    stage('Build') {
        echo 'Building....'
    }
    
    if(params.autoDeploy) {
        if(currentBuild.result==null || currentBuild.result==true) {
            if(env.BRANCH_NAME=='master' || env.BRANCH_NAME.startsWith('hotfix') {
                try {
                    timeout(time: 4, unit: 'HOURS') {
                        env.ENV_DEPLOY_NAME = input message: 'User input required',
                                ok: 'Deploy!',
                                parameters: [choice(name: 'Branch to deploy', choices: "${option1}\n${option2}\n${option3}", description: 'What Beanstalk environment you want deploy to?')]
                        echo "Env: ${env.ENV_DEPLOY_NAME} ..."
                    }
                } catch(FlowInterruptedException e) {
                    echo "Flow interrupted ${ex}"
                    currentBuild.result = "ABORTED"
                    //shouldDeploy = false
                }
            }
            
            stage('Deploy') {
                echo 'Deploying....${ENV_DEPLOY_NAME}'
            }
        }
    }
}

