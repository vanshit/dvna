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
                sh 'npm install --force'
            }

        stage ('NPM Audit Analysis') {
            steps {
                sh '/home/user/Desktop/dvna/npm-audit.sh'
            }
        }

        stage ('NodeJsScan Analysis') {
            steps {
                sh 'nodejsscan --directory `pwd` --output /{JENKINS HOME DIRECTORY}/reports/nodejsscan-report'
            }
        }

        stage ('Retire.js Analysis') {
            steps {
                sh 'retire --path `pwd` --outputformat json --outputpath /{JENKINS HOME DIRECTORY}/reports/retirejs-report --exitwith 0'
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

        stage ('Run ZAP for DAST') {
            steps {
                sh '/{PATH TO SCRIPT}/baseline-scan.sh'
            }
        }

        stage ('Run W3AF for DAST') {
            agent {
                label 'raf-vm'
            }

            steps {
                sh '/{PATH TO SCRIPT}/w3af/w3af_console -s /{PATH TO SCRIPT}/scripts/w3af_scan_script.w3af'
                sh 'scp -r /{PATH TO OUTPUT}/w3af/output-w3af.txt chaos@10.0.2.19:/{HOME DIRECTORY}/'
            }
        }

        stage ('Take DVNA offline') {
            steps {
                sh 'pm2 stop server.js'
                sh 'mv baseline-report.html /{JENKINS HOME DIRECTORY}/reports/zap-report.html'
                sh 'cp /{HOME DIRECTORY}/output-w3af.txt /{JENKINS HOME DIRECTORY}/reports/w3af-report'
            }
        }

        stage ('Lint Analysis with Jshint') {
            steps {
                sh '/{PATH TO SCRIPT}/jshint-script.sh'
            }
        }

        stage ('Lint Analysis with EsLint') {
            steps {
                sh '/{PATH TO SCRIPT}/eslint-script.sh'
            }
        }

        stage ('Generating Software Bill of Materials') {
            steps {
                sh 'cyclonedx-bom -o /{JENKINS HOME DIRECTORY}/reports/sbom.xml'
            }
        }

        stage ('Deploy to App Server') {
            steps {
                sh 'echo "Deploying App to Server"'
                sh 'ssh -o StrictHostKeyChecking=no chaos@10.0.2.20 "cd dvna && pm2 stop server.js"'
                sh 'ssh -o StrictHostKeyChecking=no chaos@10.0.2.20 "rm -rf dvna/ && mkdir dvna"'
                sh 'scp -r * chaos@10.0.2.20:~/dvna'
                sh 'ssh -o StrictHostKeyChecking=no chaos@10.0.2.20 "source ./env.sh && cd dvna && pm2 start server.js"'
            }
        }

    }

}
