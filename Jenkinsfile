pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                script { run("docker build -t myapp:${env.BUILD_NUMBER} .") }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    runIgnoreErrors("docker rm -f myapp")
                    run("docker run -d --restart unless-stopped --name myapp -p 8081:80 -e BUILD=${env.BUILD_NUMBER} myapp:${env.BUILD_NUMBER}")
                }
            }
        }
        stage('Smoke test') {
            steps {
                sleep 5
                script { run("curl -f http://localhost:8081") }
            }
        }
    }
}

def run(String cmd) {
    if (isUnix()) { sh cmd } else { bat cmd }
}

def runIgnoreErrors(String cmd) {
    if (isUnix()) { sh(script: cmd, returnStatus: true) } else { bat(script: cmd, returnStatus: true) }
}