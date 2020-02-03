#!/groovy

def workerNode = 'master'
def checkoutUrl = 'https://github.com/devingiannoni/resume'
def checkoutBranch = '*/master'

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
        def errors
        if (!errors.isEmpty()) {
            mail(
                bcc: '',
                body: "spelling errors in master: ${spellingErrors}",
                cc: '',
                charset: 'UTF-8',
                from: '',
                mimeType: 'text/html',
                replyTo: '',
                subject: "something bad happened",
                to: "devingiannoni@gmail.com"
            )
            currentBuild.result = 'FAILURE'
            error("spelling errors detected")
        }
    }


    stage('build') {
        sh "docker build -t devngee/resume:vaporwave ."
    }

    stage('push') {
        sh "docker push devngee/resume:vaporwave"
    }

    stage('deploy'){
        sh "docker stop resume-container"
        sh "docker rm resume-container"
        sh "docker pull devngee/resume:vaporwave"
        sh "docker run --name resume-container -d -p 80:80 devngee/resume:vaporwave"
    }

    stage('vars') {
        sh "docker --version"
        sh "git --version"
        sh "aspell -v"
        echo "${env.getEnvironment()}"
    }

}
