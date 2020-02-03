#!/groovy

def workerNode = 'master'
def checkoutUrl = 'https://github.com/devingiannoni/resume'
def checkoutBranch = '*/master'
def emailTo = 'devingiannoni@gmail.com'
def emailBody = ''
def emailSubject = ''
def imageRepo = 'devngee/resume'
def imageTag = 'vaporwave'
def liveContainer = 'resume-container'


node(workerNode) {

    stage('checkout') {
        try {
            checkout([
                $class: 'GitSCM', 
                branches: [[name: checkoutBranch]], 
                doGenerateSubmoduleConfigurations: false, 
                extensions: [], 
                submoduleCfg: [], 
                userRemoteConfigs: 
                [[url: checkoutUrl]]
            ])
        } catch(e) {
            sendMail()
        }
    }

    stage('tests') {
        def errors = sh (script: 'aspell --mode=html list < index.html', returnStdout: true)
        if (!errors.isEmpty()) {
            mail(
                bcc: '',
                body: "${checkoutUrl} has errors in ${checkoutBranch}: ${errors}",
                cc: '',
                charset: 'UTF-8',
                from: '',
                mimeType: 'text/html',
                replyTo: '',
                subject: "${checkoutUrl} build failed",
                to: "${emailTo}"
            )
            currentBuild.result = 'FAILURE'
            error("errors detected")
        }
    }


    stage('build') {
        try {
            sh "docker build -t ${imageRepo}:${imageTag} ."
        } 
        catch (e) {
            sendMail()
        } 
    }

    stage('push') {
        try {
            sh "docker push ${imageRepo}:${imageTag}"
        } catch (e) {
            sendMail()
        }
    }

    stage('deploy'){
        try {
            sh "docker stop ${liveContainer}"
            sh "docker rm ${liveContainer}"
            sh "docker pull ${imageRepo}:${imageTag}"
            sh "docker run --name ${liveContainer} -d -p 80:80 ${imageRepo}:${imageTag}"
        } catch (e) {
            sendMail()
        } 

    }

    stage('vars') {
        sh "docker --version"
        sh "git --version"
        sh "aspell -v"
        echo "${env.getEnvironment()}"
        sendMail()
    }

}

def sendMail() {
    mail(
        body: '${DEFAULT_SUBJECT}',
        subject: '${DEFAULT_CONTENT}',
        to: '${emailTo}'
    )
}
