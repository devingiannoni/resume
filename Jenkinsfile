#!/groovy

def branch = '*/DEVOPS-1011'
def url = 'http://bitbucket.sladmin.com/scm/dev/jenkins-umbrella.git'
def credentialsId = 'Devops.service'
def node = 'master'
def email = 'dgiannoni@somalogic.com'
def repo = 'devngee/resume'
def tag = 'vaporwave'
def container = 'resume-container'

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
}


stage('build') {
    node('master') {
        sh "docker build -t devngee/resume:vaporwave ."
    }
}

stage('push') {
    node('master') {
        sh "docker push devngee/resume:vaporwave"
    }
}

stage('deploy'){
    node('master'){
        sh "docker stop resume-container"
        sh "docker rm resume-container"
        sh "docker pull devngee/resume:vaporwave"
        sh "docker run --name resume-container -d -p 80:80 devngee/resume:vaporwave"
    }
}

stage('vars') {
   node('master') {
      sh "docker --version"
      sh "git --version"
      sh "aspell -v"
      echo "${env.getEnvironment()}"
   }
}
