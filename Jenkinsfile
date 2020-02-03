#!/groovy

def branch = '*/master'
def url = 'https://github.com/devingiannoni/resume'
def credentialsId = ''
def node = 'master'
def email = 'devingiannoni@gmail.com'
def repo = 'devngee/resume'
def tag = 'vaporwave'
def container = 'resume-container'

node (${node}) {

    stage('checkout') {
      checkout([
          $class: 'GitSCM',
          branches: [[name: ${branch}]],
          doGenerateSubmoduleConfigurations: false,
          extensions: [],
          submoduleCfg: [],
          userRemoteConfigs:
            [[
                credentialsId: '${credentialsId}', 
                url: '${url}'
            ]]
        ])
    }

    stage('tests') {
        def errors = sh (script: 'aspell --mode=html list < index.html', returnStdout: true)
        if (!errors.isEmpty()) {
            mail(
                bcc: '',
                body: "errors in ${branch}: ${errors}",
                cc: '',
                charset: 'UTF-8',
                from: '',
                mimeType: 'text/html',
                replyTo: '',
                subject: "build failed",
                to: "${email}"
            )
            currentBuild.result = 'FAILURE'
            error(${error})
        }
    }


    stage('build') {
        sh "docker build -t ${repo}:${tag} ."
    }

    stage('push') {
        sh "docker push ${repo}:${tag}"
    }

    stage('deploy'){
        sh "docker stop ${container}"
        sh "docker rm ${container}"
        sh "docker pull ${repo}:${tag}"
        sh "docker run --name ${container} -d -p 80:80 ${repo}:${tag}"
    }

    stage('vars') {
        sh "docker --version"
        sh "git --version"
        sh "aspell -v"
        echo "${env.getEnvironment()}"
    }
}
