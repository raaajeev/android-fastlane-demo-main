// //Added comment
// //folder name from repository 
// def replace_file_folder="demo"

// slack_channel = 'jenkins'
// pipeline
// {
//     agent{
// //         docker {
// //             label 'ec2'
// //             image 'ashwin7227/mobile-ci:latest'
// //         }
//         any
//     }
//     stages{
//         stage('Init')
//         {
//              when{
//                     expression {
//                         return env.shouldBuild != "false"
//                     }
//             }//when
            
//              steps {
//                 script {
//                     lastCommitInfo = sh(script: "git log -1", returnStdout: true).trim()
//                     commitContainsSkip = sh(script: "git log -1 | grep 'skip ci'", returnStatus: true)
//                     //slackMessage = "*ANDROID:${env.JOB_NAME}* *${env.BRANCH_NAME}* received a new commit. \nHere is commmit info: ${lastCommitInfo}\n*Console Output*: <${BUILD_URL}/console | (Open)> Username :   Password: \n :hourglass_flowing_sand::hourglass_flowing_sand:"

//                     //send slack notification of new commit
//                     //slack_send(slackMessage)

//                     //if commit message contains skip ci
//                      if(commitContainsSkip == 0) {
//                         skippingText = " Skipping Build for *${env.BRANCH_NAME}* branch."
//                         env.shouldBuild = false
//                         currentBuild.result = 'ABORTED'
//                         //slack_send(skippingText,"warning")
//                         error('BUILD SKIPPED') 
//                     }
//                 }
//             }//step end
//         }
//         stage('Build')
//         {
//             when{
//                 expression {
//                     return env.shouldBuild != "false"
//                 }
//             }
//             stages{
//                 stage('preparation')
//                 {
//                     steps{
//                         script
//                             {
//                                 branch_name = "${env.BRANCH_NAME}"
                            
//                                 if(branch_name == "master")
//                                 {
//                                     //temp added staging credentials. Change when release . [Need Update]
//                                     env.BUILD_TYPE = 'debug'
//                                     env.BUILD_FOLDER='debug'
                                    
//                                 }else if(branch_name == "development")
//                                 {
//                                     env.BUILD_TYPE = 'development'
//                                     env.BUILD_FOLDER='debug'
                    
//                                 }else if(branch_name == "staging")
//                                 {
//                                     env.BUILD_TYPE = 'staging'
//                                     env.BUILD_FOLDER='staging'
//                                 }

//                             }//script
//                     }//steps
//                 }//preparation

//                 stage('Build Application')
//                 {   
//                     steps{
//                         //change permission of gradlew and Gemfile
//                         sh "chmod +x gradlew"
//                         sh "chmod +x Gemfile"

//                         //slack_send(" :crossed_fingers:  Building *${env.BUILD_TYPE}* variant.")

//                         //version code increment
//                         //sh "fastlane increase firebase_version_code --env ${env.BUILD_TYPE}"

//                         // build and upload to firebase
//                         sh "fastlane ${env.BUILD_TYPE} --env ${env.BUILD_TYPE}"

//                     }//steps end
//                 }//build application step

//             }//nested stages

//         }//build stage
//         stage('Clean')
//         {
//             when{
//                 expression {
//                     return env.shouldBuild != "false"
//                 }
//             }
//             steps{
               
//                 sh 'fastlane clean'
//             }
//         }//clean build

//     }//stages

//     post {
//         always {
//           //  sh "rsync -au ${HOME}/.gradle/caches ${HOME}/.gradle/wrapper ${GRADLE_CACHE}/ || true"

//             sh "chmod -R 777 ."
//             deleteDir() 
//         }
//         success{
//             sh 'echo "success"'
//              //slack_send("Build for *${env.BRANCH_NAME}* completed successfully. ${env.BUILD_TYPE} variant uploaded successfully to firebase distribution.","#0066ff")
//         }
//         failure {
//             sh 'echo "failure"'
//             //slack_send("*${env.BRANCH_NAME}* Build Failed. \nHere is commmit info: ${lastCommitInfo}\n*Console Output*: <${BUILD_URL}/console | (Open)> Username :    Password: \n","danger")
//         }
//     }

// }//pipeline


// //change channel name
// def slack_send(slackMessage,messageColor="good")
// {
//     slackSend channel: slack_channel , color: messageColor, message: slackMessage
// }
pipeline {
agent { label 'mac-mini-slave' }
parameters {
  // the default choice for commit-triggered builds is the first item in the choices list
  choice(name: 'buildVariant', choices: ['Debug_Scan_Only', 'Debug_TestFlight', 'Release_AppStore_TestFlight'], description: 'The variants to build')
 }
environment {...}
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
