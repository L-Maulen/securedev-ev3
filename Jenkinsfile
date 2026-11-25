pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh '''
                    python3 -m venv .venv
                    .venv/bin/pip install -q -r requirements.txt pytest
                '''
            }
        }

        stage('Seguridad (SAST y SCA)') {
            steps {
                sh '''
                    bandit -ll -f txt -o reporte-bandit.txt app.py || true
                    cat reporte-bandit.txt
                    pip-audit -r requirements.txt -f json -o reporte-pip-audit.json || true
                    pip-audit -r requirements.txt || true
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    if [ -d "tests" ]; then
                        .venv/bin/python -m pytest -q
                    else
                        .venv/bin/python -m py_compile app.py
                        echo "Sintaxis de app.py verificada correctamente."
                    fi
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'reporte-bandit.txt, reporte-pip-audit.json', allowEmptyArchive: true
        }
    }
}
