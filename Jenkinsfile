pipeline {
    agent {
        docker {
            image 'python:3.9-slim'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    environment {
        SONARQUBE_TOKEN = credentials(sqp_e660e5487e87ae14834f50cf1aceead959152ad4) // Usa el ID de la credencial
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/andresangarita0922/mi-proyecto-python.git'
            }
        }
        stage('Instalar dependencias') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }
        stage('Ejecutar pruebas unitarias') {
            steps {
                sh 'pytest tests/'
            }
        }
        stage('Análisis con SonarQube') {
            steps {
                withSonarQubeEnv('SonarQube-Server') {
                    sh """
                        sonar-scanner \
                        -Dsonar.projectKey=mi-proyecto-python \
                        -Dsonar.python.version=3.9 \
                        -Dsonar.sources=src/ \
                        -Dsonar.host.url=${SONARQUBE_URL} \
                        -Dsonar.login=${SONARQUBE_TOKEN}
                    """
                }
            }
        }
        stage('Verificar Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}