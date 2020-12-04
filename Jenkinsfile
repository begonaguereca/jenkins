pipeline {
    agent {
        node {
            label 'linux'
        }
    }
    triggers {
        cron('H */4 * * 1-5')
    }
    environment {
        CC = 'clang'
        BUILD_SESSION_ENV = sh (
            script: 'printenv |sort',
            returnStdout: true
        ).trim()
    }
    stages {
        stage('Build') {
            steps {
                echo "Building ${env.BUILD_ID} on ${env.JENKINS_URL}"
                echo "Testing env vars: ${env.CC}"
                git credentialsId: 'jenkout-bot', url: 'https://github.com/github/jenkout-fake-repo'
                withCredentials([usernamePassword(credentialsId: 'hello-kb', passwordVariable: 'pass', usernameVariable: 'user')]) {
                    echo "Hello...somebody? Testing: ${env.user} ${env.pass}"
                }
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
                sh 'true'
                git credentialsId: 'stage-test', url: 'https://github.com/github/test-stage'

                // Write an useful file, which is needed to be archived.
                writeFile(file: 'output/usefulfile.txt', text: "This file is useful, need to archive it. Git SHA: ${env.GIT_COMMIT}")

                writeFile(file: "output/env.txt", text: env.BUILD_SESSION_ENV)

                // Write an useless file, which is not needed to be archived.
                writeFile(file: 'output/uselessfile.md', text: 'This file is useless, no need to archive it.')

                // Archive the build output artifacts.
                archiveArtifacts(artifacts: 'output/*.txt', excludes: 'output/*.md')
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}
