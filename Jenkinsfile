pipeline {
    agent any

    options {
        skipStagesAfterUnstable()
    }

    stages {
        stage('Prepare') {
            steps {
                sh '''
                if ! command -v python3 &> /dev/null; then
                    echo "Installing Python 3..."
                    if command -v apt-get &> /dev/null; then
                        sudo apt-get update -qq
                        sudo apt-get install -y python3 python3-pip python3-venv
                    elif command -v yum &> /dev/null; then
                        sudo yum install -y python3 python3-pip
                    elif command -v dnf &> /dev/null; then
                        sudo dnf install -y python3 python3-pip
                    else
                        echo "Unsupported package manager. Please install Python 3 manually."
                        exit 1
                    fi
                fi
                python3 --version
                pip3 --version || true
                '''
            }
        }

        stage('Build') {
            steps {
                sh 'python3 -m py_compile sources/add2vals.py sources/calc.py'
            }
        }

        stage('Test') {
            steps {
                sh '''
                pip3 install --user pytest
                mkdir -p test-reports
                pytest -v --junitxml=test-reports/results.xml sources/test_calc.py
                '''
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }

        stage('Deliver') {
            steps {
                sh '''
                pip3 install --user pyinstaller
                pyinstaller --onefile sources/add2vals.py
                '''
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
    }
}
