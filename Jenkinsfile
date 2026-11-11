pipeline {
    agent any

    triggers {
        pollSCM('H/2 * * * *')
    }

    stages {
        stage('Build') {
            steps {
                script { run("docker build -t myapp:${env.BUILD_NUMBER} .") }
            }
        }

        stage('Test') {
            steps {
                script { run("docker run --rm myapp:${env.BUILD_NUMBER} php -l /var/www/html/index.php") }
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
                script { run("curl -fs http://localhost:8081 | grep 'Build: ${env.BUILD_NUMBER}'") }
            }
        }
    }

    post {
        success { echo "Build ${env.BUILD_NUMBER} deployed successfully" }
        failure { echo "Build ${env.BUILD_NUMBER} failed, old version is still running" }
    }
}

def run(String cmd) {
    if (isUnix()) { sh cmd } else { bat cmd }
}

def runIgnoreErrors(String cmd) {
    if (isUnix()) { sh(script: cmd, returnStatus: true) } else { bat(script: cmd, returnStatus: true) }
}
