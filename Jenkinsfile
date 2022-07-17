pipeline {
    agent { label 'MASTER'}
    triggers {
        cron('H * * * *')
        pollSCM('* * * * *')
    }
    parameters {
        string(name: 'BRANCH', defaultValue: 'master', description: 'Branch to build' )
        choice(name: 'GOAL', choices: ['package', 'clean package', 'install'], description: 'maven goals')
    }
    options {
        timeout(time: 1, unit: 'HOURS')
        retry(2)
    }
    environment {
        CI_ENV = 'DEV'
    }
    stages {
        stage('scm') {
            environment {
                DUMMY = 'FUN'
            }
            steps {
                mail subject: "BUILD Started on branch ${env.GIT_BRANCH} with it build id ${env.BUILD_ID}", to: 'devops@aipl.com', from: 'jenkins@aipl.com', body: 'EMPTY BODY'
                git branch: "${params.BRANCH}", url: 'https://github.com/spinfo159/gameoflife.git'
                //input message: 'Continue to next stage? ', submitter: 'qtaws,qtazure'
                echo env.CI_ENV
                echo env.DUMMY
            }
        }
        stage('build') {
            steps {
                echo env.GIT_URL
                timeout(time:10, unit: 'MINUTES') {
                    sh "mvn ${params.GOAL}"
                }
                
            }
        }
    
        }
    }
    post {
        success {
           archiveArtifacts '**/*.war'
            junit '**/TEST-*.xml'
            mail subject: 'BUILD Completed Successfully '+env.BUILD_ID, to: 'devops@aipl.com', from: 'jenkins@aipl.com', body: 'EMPTY BODY succss sction'
        }
        failure {
            mail subject: 'BUILD Failed '+env.BUILD_ID+'URL is '+env.BUILD_URL, to: 'devops@aipl.com', from: 'jenkins@aipl.com', body: 'EMPTY BODY failure session'
        }
        always {
            echo "Finished"
        }
        changed {
            echo "Changed"
        }
        unstable {
            mail subject: 'BUILD Unstable '+env.BUILD_ID+'URL is '+env.BUILD_URL, to: 'devops@aipl.com', from: 'jenkins@aipl.com', body: 'EMPTY BODY'

        }
    }
}