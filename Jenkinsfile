pipeline {
    agent any
    
    environment {
        APP_URL = 'http://localhost:8080'
    }
    
    stages {
        stage('Preparación') {
            steps {
                // Crear directorio para reportes
                sh 'mkdir -p reports'
                
                // Asegurar que tenemos la imagen de ZAP
                sh 'docker pull owasp/zap2docker-stable'
            }
        }
        
        stage('Deploy App') {
            steps {
                // Instalar dependencias y ejecutar app
                sh 'pip install -r requirements.txt'
                sh 'python src/main.py &'
                // Esperar a que la app esté lista
                sh 'sleep 30'
            }
        }

        stage('Baseline Scan') {
            steps {
                script {
                    sh '''
                        docker run --rm \
                        -v ${WORKSPACE}/reports:/zap/reports:rw \
                        owasp/zap2docker-stable zap-baseline.py \
                        -t ${APP_URL} \
                        -J baseline-report.json \
                        -r baseline-report.html || true
                    '''
                }
            }
        }

        stage('Full Scan') {
            steps {
                script {
                    sh '''
                        docker run --rm \
                        -v ${WORKSPACE}/reports:/zap/reports:rw \
                        owasp/zap2docker-stable zap-full-scan.py \
                        -t ${APP_URL} \
                        -J full-scan-report.json \
                        -r full-scan-report.html || true
                    '''
                }
            }
        }

        stage('Publish Reports') {
            steps {
                // Publicar reportes HTML
                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'reports',
                    reportFiles: 'baseline-report.html, full-scan-report.html',
                    reportName: 'ZAP Security Reports'
                ])
                
                // Archivar reportes JSON
                archiveArtifacts artifacts: 'reports/*.json', fingerprint: true
            }
        }
    }
    
    post {
        always {
            // Limpiar procesos
            sh 'pkill -f "python src/main.py" || true'
        }
    }
}

