pipeline {

    agent any

    stages {

        stage ('Initialization') {
            steps {
                sh 'echo "Starting the build"'
            }
        }

        stage ('Build') {
            steps {
                sh 'npm install'
            }
        }


        stage ('NPM Audit Analysis') {
            steps {
                sh 'npm audit'
            }
        }


        stage ('Dependency-Check Analysis') {
            steps {
                sh '/{JENKINS HOME DIRECTORY}/dependency-check/bin/dependency-check.sh --scan `pwd` --format JSON --out /{JENKINS HOME DIRECTORY}/reports/dependency-check-report --prettyPrint'
            }
        }

        stage ('Audit.js Analysis') {
            steps {
                sh '/{PATH TO SCRIPT}/auditjs.sh'
            }
        }

        stage ('Snyk Analysis') {
            steps {
                sh '/{PATH TO SCRIPT}/snyk.sh'
            }
        }

        stage ('Building DVNA') {
            steps {
                sh '''
                    source /{PATH TO SCRIPT}/env.sh
                    pm2 start server.js
                '''
            }
        }

}
