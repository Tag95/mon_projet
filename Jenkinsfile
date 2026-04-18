pipeline {
    agent any
    environment {
        PYTHONPATH = "${WORKSPACE}"
    }
    stages {
        stage('Setup') {
            steps {
                sh '''
                    python3 -m venv venv
                    venv/bin/pip install --upgrade pip
                    venv/bin/pip install -r requirements.txt
                '''
            }
        }
        stage('Tests unitaires') {
            steps {
                sh 'PYTHONPATH=${WORKSPACE} venv/bin/pytest tests/ -v'
            }
        }
        stage('SAST - Bandit') {
            steps {
                sh 'venv/bin/bandit -r src/ -ll || true'
            }
        }
        stage('Secrets Scan - Gitleaks') {
            steps {
                sh 'gitleaks detect -s . -v || true'
            }
        }
        stage('Docker Build + Scan') {
            steps {
                sh '''
                    docker build -t mon_app:latest .
                    trivy image mon_app:latest || true
                '''
            }
        }
        stage('Clean') {
            steps {
                sh 'rm -rf venv'
            }
        }
    }
    post {
        success {
            echo "Pipeline DevSecOps réussi"
        }
        failure {
            echo "Pipeline échoué - faille détectée"
        }
    }
}
