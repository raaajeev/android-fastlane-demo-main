pipeline {
agent { label 'mac-mini-slave' }
parameters {
  // the default choice for commit-triggered builds is the first item in the choices list
  choice(name: 'buildVariant', choices: ['Debug_Scan_Only', 'Debug_TestFlight', 'Release_AppStore_TestFlight'], description: 'The variants to build')
 }
environment {
LC_ALL = 'en_US.UTF-8'
APP_NAME = 'AdManagerTest'
BUILD_NAME = 'AdManagerTest'
APP_TARGET = 'AdManagerTest'
APP_PROJECT = 'AdManagerTest.xcodeproj'
APP_WORKSPACE = 'AdManagerTest.xcworkspace'
APP_TEST_SCHEME = 'AdManagerTest'
PUBLISH_TO_CHANNEL = 'teams'
}
stages {
//<< Git SCM Checkout >>
  stage('Git Checkout') {
   steps {
    checkout scm
   }
  }
stage('Update Env with Build Variant') {
   steps {
    script {
     env.BUILD_VARIANT = params.buildVariant
     // Conditionally define a build variant 'impact'
     if (BUILD_VARIANT == 'Debug_TestFlight') {
      echo "Debug_TestFlight"
     } else if (BUILD_VARIANT == 'Release_AppStore_TestFlight') {
      echo "Release_AppStore_TestFlight"
     }
    }
   }
  }
stage('Git - Fetch Version/Commits') {
   steps {
    script {
    //Shell commands
env.GIT_COMMIT_MSG = sh(returnStdout: true, script: '''
     git log -1 --pretty=%B ${GIT_COMMIT}
     ''').trim()
def DATE_TIME = sh(returnStdout: true, script: '''
                    date +%Y.%m.%d-%H:%M:%S
                    ''').trim()
....
    }
   }
  }
stage('Unit Test cases') {
}
stage('Quality checks - Report') {
   parallel {
    stage('Linting') { ... }
    stage('Code Coverage') { ... }
   }
stage('Commit File changes') { ... }
stage('Build') { ... }
stage('Generating Artifacts') { ... }
stage('Post Build -- Actions') { ... }
}
post {
success {
office365ConnectorSend color: '#86BC25', status: currentBuild.result, webhookUrl: "${ env.WEBHOOK_URL }",
message: "Test Successful: ${JOB_NAME} - ${currentBuild.displayName}<br>Pipeline duration: ${currentBuild.durationString.replace(' and counting', '')}"
}
unstable {
   office365ConnectorSend color: '#FFE933', status: currentBuild.result, webhookUrl: "${ env.WEBHOOK_URL }",
   message: "Successfully Build but Unstable. Unstable means test failure, code violation, push to remote failed etc. : ${JOB_NAME} - ${currentBuild.displayName}<br>Pipeline duration: ${currentBuild.durationString.replace(' and counting', '')}"
  }
failure {
office365ConnectorSend color: '#ff0000', status: currentBuild.result, webhookUrl: "${ env.WEBHOOK_URL }",
message: "Build Failed: ${JOB_NAME} - ${currentBuild.displayName}<br>Pipeline duration: ${currentBuild.durationString.replace(' and counting', '')}"
}
always {
echo "Build completed with status: ${currentBuild.result}"
}
}
