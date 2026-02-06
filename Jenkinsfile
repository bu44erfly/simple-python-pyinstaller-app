pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:3.10-slim'
                    args '-u root'
                }
            }
            steps {
                sh '''
                    python -m py_compile sources/add2vals.py sources/calc.py
                '''
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'python:3.10-slim'
                    args '-u root'
                }
            }
            steps {
                sh '''
                    pip install pytest pytest-cov
                    mkdir -p test-reports
                    py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py
                '''
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }

        stage('Deliver') { 
            agent {
                docker {
                    image 'python:3.10-slim'
                    args '-u root'
                }
            }
            steps {
                sh '''
                    apt-get update && apt-get install -y binutils gcc
                    pip install pyinstaller
                    pyinstaller --onefile sources/add2vals.py
                '''
            }
            post {
                success {
                    archiveArtifacts artifacts: 'dist/add2vals', fingerprint: true
                }
            }
        }
    }
}
