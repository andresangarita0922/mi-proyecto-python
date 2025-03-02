pipeline {
    agent any
    
    environment {
        APP_URL = 'http://localhost:8080'  
    }
    
    stages {
        stage('OWASP ZAP - Baseline Scan') {
            steps {
                script {
                    sh """
                        sudo docker run --rm \
                        -v \$(pwd):/zap/wrk/:rw \
                        -t owasp/zap2docker-stable \
                        zap-baseline.py -t ${APP_URL} \
                        -r baseline-report.html \
                        -I
                    """
                }
                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: '.',
                    reportFiles: 'baseline-report.html',
                    reportName: 'ZAP Baseline Report'
                ])
            }
        }
        
        stage('OWASP ZAP - Full Scan') {
            steps {
                script {
                    sh """
                        sudo docker run --rm \
                        -v \$(pwd):/zap/wrk/:rw \
                        -t owasp/zap2docker-stable \
                        zap-full-scan.py -t ${APP_URL} \
                        -r full-scan-report.html \
                        -I
                    """
                }
                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: '.',
                    reportFiles: 'full-scan-report.html',
                    reportName: 'ZAP Full Scan Report'
                ])
            }
        }
    }
}
