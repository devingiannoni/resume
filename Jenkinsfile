#!/groovy

stage('checkout') {
    node('master') {
        checkout(
        [
            $class: 'GitSCM', 
            branches: [[name: '*/master']], 
            doGenerateSubmoduleConfigurations: false, 
            extensions: [], 
            submoduleCfg: [], 
            userRemoteConfigs: [[url: 'https://github.com/devingiannoni/resume']]
        ]
      )
   }
}
stage('tests') {
    node('master') {
        def spellingErrors = sh (script: 'aspell --mode=html list < index.html', returnStdout: true)
        if (!spellingErrors.isEmpty()) {
            echo "tests failed"
        }
    }
}


stage('docker build') {
    node('master') {
        try {
            echo "build fired"
            // unstash name: "artifacts"
            // image = docker.build('artifactory:5001/acts:' + pom.version)
        } catch (e) {
            notifyOnError()
            throw e
        }
    }
}

stage('docker push') {
    node('master') {
        try {
            echo "push fired"
        } catch (e) {
            notifyOnError()
            throw e
        }
    }
}

stage('k8 deploy'){
    node('master'){
        try {

        }
        catch (e) {
            notifyOnError()
            throw e
        }
    }
}

stage('echo vars') {
   node('master') {
      sh "docker --version"
      sh "git --version"
      echo "BRANCH_NAME ${env.BRANCH_NAME}"
      echo "CHANGE_ID ${env.CHANGE_ID}"
      echo "CHANGE_URL ${env.CHANGE_URL}"
      echo "CHANGE_TITLE ${env.CHANGE_TITLE}"
      echo "CHANGE_AUTHOR ${env.CHANGE_AUTHOR}"
      echo "CHANGE_AUTHOR_DISPLAY_NAME ${env.CHANGE_AUTHOR_DISPLAY_NAME}"
      echo "CHANGE_AUTHOR_EMAIL ${env.CHANGE_AUTHOR_EMAIL}"
      echo "CHANGE_TARGET ${env.CHANGE_TARGET}"
      echo "CHANGE_BRANCH ${env.CHANGE_BRANCH}"
      echo "CHANGE_FORK ${env.CHANGE_FORK}"
      echo "BUILD_NUMBER ${env.BUILD_NUMBER}"
      echo "BUILD_ID ${env.BUILD_ID}"
      echo "BUILD_DISPLAY_NAME ${env.BUILD_DISPLAY_NAME}"
      echo "JOB_NAME ${env.JOB_NAME}"
      echo "JOB_BASE_NAME ${env.JOB_BASE_NAME}"
      echo "BUILD_TAG ${env.BUILD_TAG}"
      echo "EXECUTOR_NUMBER ${env.EXECUTOR_NUMBER}"
      echo "NODE_NAME ${env.NODE_NAME}"
      echo "NODE_LABELS ${env.NODE_LABELS}"
      echo "WORKSPACE ${env.WORKSPACE}"
      echo "JENKINS_HOME ${env.JENKINS_HOME}"
      echo "JENKINS_URL ${env.JENKINS_URL}"
      echo "BUILD_URL ${env.BUILD_URL}"
      echo "JOB_URL ${env.JOB_URL}"
      echo "GIT_COMMIT ${env.GIT_COMMIT}"
      echo "GIT_PREVIOUS_COMMIT ${env.GIT_PREVIOUS_COMMIT}"
      echo "GIT_PREVIOUS_SUCCESSFUL_COMMIT ${env.GIT_PREVIOUS_SUCCESSFUL_COMMIT}"
      echo "GIT_BRANCH ${env.GIT_BRANCH}"
      echo "GIT_LOCAL_BRANCH ${env.GIT_LOCAL_BRANCH}"
      echo "GIT_CHECKOUT_DIR ${env.GIT_CHECKOUT_DIR}"
      echo "GIT_URL ${env.GIT_URL}"
      echo "GIT_COMMITTER_NAME ${env.GIT_COMMITTER_NAME}"
      echo "GIT_AUTHOR_NAME ${env.GIT_AUTHOR_NAME}"
      echo "GIT_COMMITTER_EMAIL ${env.GIT_COMMITTER_EMAIL}"
      echo "GIT_AUTHOR_EMAIL ${env.GIT_AUTHOR_EMAIL}"
      echo "SVN_REVISION ${env.SVN_REVISION}"
      echo "SVN_URL ${env.SVN_URL}"
   }
}
// def notifyOnFixed() {
//     def currentProblem = currentBuild?.result != 'SUCCESS'
//     def previousProblem = currentBuild?.previousBuild?.result != 'SUCCESS'
//     if((currentProblem || previousProblem) && env.BRANCH_NAME == "master") {
//         sendMail()
//     }
// }

// def notifyOnError() {
//     currentBuild.result="FAILED"
//     if (env.BRANCH_NAME == "master") {
//         sendMail()
//     }
// }

// def sendMail() {
//     emailext to: 'softwareteam@somalogic.com',
//             subject: '${DEFAULT_SUBJECT}',
//             body: '${DEFAULT_CONTENT}'
// }
