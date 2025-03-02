pipeline {
    agent any
    
    environment {
        ZAP_HOME = tool 'owasp-zap'
        APP_URL = 'http://localhost:8080'  
    }
    
    stages {
        stage('OWASP ZAP - Baseline Scan') {
            steps {
                script {
                    sh """
                        ${ZAP_HOME}/zap-baseline.py -t ${APP_URL} \
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
    }
}
