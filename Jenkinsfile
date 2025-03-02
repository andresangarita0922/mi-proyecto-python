pipeline {
    agent any
    
    environment {
        APP_URL = 'http://localhost:8080'
        ZAP_HOME = '/opt/zaproxy'  // Ruta de ZAP en Linux
    }
    
    stages {
        stage('Preparación') {
            steps {
                sh 'mkdir -p reports'
            }
        }
        
        stage('Deploy App') {
            steps {
                sh 'pip install -r requirements.txt'
                sh 'python src/main.py &'
                sh 'sleep 30'
            }
        }

        stage('Baseline Scan') {
            steps {
                script {
                    sh """
                        cd ${ZAP_HOME}
                        ./zap.sh -cmd -quickurl ${APP_URL} -quickprogress -quickout ${WORKSPACE}/reports/baseline-report.html
                    """
                }
            }
        }

        stage('Full Scan') {
            steps {
                script {
                    sh """
                        cd ${ZAP_HOME}
                        ./zap.sh -cmd -fullurl ${APP_URL} -o -dir ${WORKSPACE}/reports -report full-scan-report.html
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
            sh 'pkill -f "python src/main.py" || true'
        }
    }
}
