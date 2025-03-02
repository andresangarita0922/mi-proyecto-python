pipeline {
    agent any
    
    environment {
        APP_URL = 'http://localhost:8080'
        ZAP_HOME = tool 'OWASP_ZAP'
    }
    
    stages {
        stage('Preparar Entorno') {
            steps {
                sh 'python -m pip install -r requirements.txt'
                sh 'python src/main.py &'
            }
        }
        
        stage('Baseline Scan') {
            steps {
                sh """
                    docker run -v \$(pwd)/reports:/zap/reports owasp/zap2docker-stable zap-baseline.py \
                    -t ${APP_URL} \
                    -r baseline-report.html
                """
            }
        }
        
        stage('Full Scan') {
            steps {
                sh """
                    docker run -v \$(pwd)/reports:/zap/reports owasp/zap2docker-stable zap-full-scan.py \
                    -t ${APP_URL} \
                    -r full-scan-report.html
                """
            }
        }
        
        stage('Publicar Reportes') {
            steps {
                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'reports',
                    reportFiles: 'baseline-report.html, full-scan-report.html',
                    reportName: 'ZAP Security Reports'
                ])
            }
        }
    }
}
