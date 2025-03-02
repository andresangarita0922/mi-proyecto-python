pipeline {
    agent any
    
    environment {
        APP_URL = 'http://localhost:8080'
        ZAP_HOME = 'C:\\Program Files\\OWASP\\Zed Attack Proxy'
    }
    
    stages {
        stage('Preparación') {
            steps {
                bat 'mkdir reports'
            }
        }
        
        stage('Deploy App') {
            steps {
                bat 'pip install -r requirements.txt'
                bat 'start /B python src/main.py'
                bat 'timeout /t 30'
            }
        }

        stage('Baseline Scan') {
            steps {
                script {
                    bat """
                        cd "${ZAP_HOME}"
                        zap.bat -cmd -quickurl ${APP_URL} -quickprogress -quickout %WORKSPACE%\\reports\\baseline-report.html
                    """
                }
            }
        }

        stage('Full Scan') {
            steps {
                script {
                    bat """
                        cd "${ZAP_HOME}"
                        zap.bat -cmd -fullurl ${APP_URL} -o -dir %WORKSPACE%\\reports -report full-scan-report.html
                    """
                }
            }
        }

        stage('Publish Reports') {
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
    
    post {
        always {
            bat 'taskkill /F /IM python.exe /FI "WINDOWTITLE eq src/main.py" || exit 0'
        }
    }
}

