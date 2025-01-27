pipeline {
    agent any

    environment {
        PYTHON_VERSION = '3.9'
        VENV_PATH = '.venv' // Virtual environment directory
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    git url: 'https://github.com/NehaManasvi/pyvolt.git', branch: 'master'
                }
            }
        }
        stage('Set Up Python') {
            steps {
                script {
                    sh """
                    sudo apt-get update
                    sudo apt-get install -y python${env.PYTHON_VERSION} python${env.PYTHON_VERSION}-venv python${env.PYTHON_VERSION}-distutils
                    curl -sS https://bootstrap.pypa.io/get-pip.py | python${env.PYTHON_VERSION}
                    export PATH=\$PATH:/var/lib/jenkins/.local/bin
                    """
                }
            }
        }
        stage('Create Virtual Environment') {
            steps {
                script {
                    sh """
                    python${env.PYTHON_VERSION} -m venv ${env.VENV_PATH}
                    . ${env.VENV_PATH}/bin/activate
                    pip install --upgrade pip
                    """
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                script {
                    sh """
                    . ${env.VENV_PATH}/bin/activate
                    pip install flake8 pytest
                    if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
                    """
                }
            }
        }
        stage('Run NV Powerflow') {
            steps {
                script {
                    sh """
                    . ${env.VENV_PATH}/bin/activate
                    python -m examples.quickstart.run_nv_powerflow
                    """
                }
            }
        }
        stage('Run NV State Estimator') {
            steps {
                script {
                    sh """
                    . ${env.VENV_PATH}/bin/activate
                    python -m examples.quickstart.run_nv_state_estimator
                    """
                }
            }
        }
        stage('Test Switch NV Powerflow') {
            steps {
                script {
                    sh """
                    . ${env.VENV_PATH}/bin/activate
                    python -m examples.quickstart.test_switch_nv_powerflow
                    """
                }
            }
        }
        stage('Test Switch NV State Estimator') {
            steps {
                script {
                    sh """
                    . ${env.VENV_PATH}/bin/activate
                    python -m examples.quickstart.test_switch_nv_state_estimator
                    """
                }
            }
        }
        stage('Lint with Flake8') {
            steps {
                script {
                    sh """
                    . ${env.VENV_PATH}/bin/activate
                    flake8 . --exclude=.venv --count --select=E9,F63,F7,F82 --show-source --statistics
                    flake8 . --exclude=.venv --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics
                    """
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
        failure {
            echo 'Pipeline failed. Please review the logs.'
        }
    }
}
