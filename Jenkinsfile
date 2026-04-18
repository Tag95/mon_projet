pipeline {
    agent any
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
                sh 'venv/bin/pytest tests/'
            }
        }
        stage('SAST - Bandit') {
            steps {
                sh 'python3 -m bandit -r src/ -ll'
            }
        }
        stage('Secrets Scan - Gitleaks') {
            steps {
                sh 'gitleaks detect -s . -v'
            }
        }
        stage('Docker Build + Scan') {
            steps {
                sh '''
                    docker build -t mon_app:latest .
                    trivy image mon_app:latest
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
