#!/groovy

echo "${env.getEnvironment()}"

def workerNode      = 'master'
def checkoutUrl     = 'https://github.com/devingiannoni/resume'
def checkoutBranch  = 'master'
def emailTo         = 'devingiannoni@gmail.com'
def emailBody       = 'final test'
def emailSubject    = 'final test'
def imageRepo       = 'devngee/resume'
def imageTag        = 'vaporwave'
def liveContainer   = 'resume-container'


node(workerNode) {

    stage('checkout') {
        try {
            checkout([
                $class: 'GitSCM', 
                branches: [[name: "*/${checkoutBranch}"]], 
                doGenerateSubmoduleConfigurations: false, 
                extensions: [], 
                submoduleCfg: [], 
                userRemoteConfigs: 
                [[url: checkoutUrl]]
            ])
        } catch(e) {
            sendMail(emailTo, emailSubject, emailBody)
        }
    }

    stage('tests') {
        try {
            def errors = sh (script: 'aspell --mode=html list < index.html', returnStdout: true)
            if (!errors.isEmpty()) {
                sendMail(emailTo, emailSubject, emailBody)
                currentBuild.result = 'FAILURE'
            }
        } catch (e) {
            sendMail(emailTo, emailSubject, emailBody)
        }
        

    }


    stage('build') {
        try {
            sh "docker build -t ${imageRepo}:${imageTag} ."
        } 
        catch (e) {
            sendMail(emailTo, emailSubject, emailBody)
        } 
    }

    stage('push') {
        try {
            sh "docker push ${imageRepo}:${imageTag}"
        } catch (e) {
            sendMail(emailTo, emailSubject, emailBody)
        }
    }

    stage('deploy'){
        try {
            sh "docker stop ${liveContainer}"
            sh "docker rm ${liveContainer}"
            sh "docker pull ${imageRepo}:${imageTag}"
            sh "docker run --name ${liveContainer} -d -p 80:80 ${imageRepo}:${imageTag}"
        } catch (e) {
            sendMail(emailTo, emailSubject, emailBody)
        } 

    }
}

def sendMail(emailTo, emailSubject, emailBody) {
            mail(
                body: "${emailBody}",
                subject: "${emailSubject}",
                to: "${emailTo}"
            )
}
