#!/groovy

def workerNode = 'master'
checkoutUrl = 'https://github.com/devingiannoni/resume'

node(workerNode) {

    stage('checkout') {
        checkout
        ([
            $class: 'GitSCM', 
            branches: [[name: '*/master']], 
            doGenerateSubmoduleConfigurations: false, 
            extensions: [], 
            submoduleCfg: [], 
            userRemoteConfigs: [[url: $checkoutUrl]]
        ])
    }

    stage('tests') {
        def spellingErrors = sh (script: 'aspell --mode=html list < index.html', returnStdout: true)
        if (!spellingErrors.isEmpty()) {
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
