#!/groovy

def workerNode = 'master'
def checkoutUrl = 'https://github.com/devingiannoni/resume'
def checkoutBranch = '*/master'
def emailTo = 'devingiannoni@gmail.com'
def imageRepo = 'devngee/resume'
def imageTag = 'vaporwave'
def liveContainer = 'resume-container'

def emailBody = ''
def emailSubject = ''

node(workerNode) {

    stage('checkout') {
        checkout([
            $class: 'GitSCM', 
            branches: [[name: checkoutBranch]], 
            doGenerateSubmoduleConfigurations: false, 
            extensions: [], 
            submoduleCfg: [], 
            userRemoteConfigs: 
            [[url: checkoutUrl]]
        ])
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
        sh "docker build -t ${imageRepo}:${imageTag} ."
    }

    stage('push') {
        sh "docker push ${imageRepo}:${imageTag}"
    }

    stage('deploy'){
        sh "docker stop ${liveContainer}"
        sh "docker rm ${liveContainer}"
        sh "docker pull ${imageRepo}:${imageTag}"
        sh "docker run --name ${liveContainer} -d -p 80:80 ${imageRepo}:${imageTag}"
    }

    stage('vars') {
        sh "docker --version"
        sh "git --version"
        sh "aspell -v"
        echo "${env.getEnvironment()}"
    }

}

def sendMail() {
    mail(
        bcc: '',
        body: "${emailBody}",
        cc: '',
        charset: 'UTF-8',
        from: '',
        mimeType: 'text/html',
        replyTo: '',
        subject: "${emailSubject}",
        to: "${emailTo}"
    )
}
