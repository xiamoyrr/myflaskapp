pipeline {
    agent any

    environment {
        // 指向 D:\code\venvs\myflaskapp 这个 venv（纯 ASCII 路径，避免 Jenkins 服务读中文路径乱码）
        PYTHON_PATH = 'D:\\code\\venvs\\myflaskapp\\Scripts\\python.exe'
    }

    stages {
        stage('Verify Python Path') {
            steps {
                bat """
                    @echo off
                    echo === Verify Python path and version ===
                    "${PYTHON_PATH}" --version
                """
            }
        }
        stage('Checkout') {
            steps {
                // TODO: 换成你自己的仓库地址
                git url: 'https://github.com/<your-account>/myflaskapp.git', branch: 'main'
            }
        }
        stage('Fix pip') {
            steps {
                bat """
                    @echo off
                    echo === Ensure pip is available ===
                    "${PYTHON_PATH}" -m ensurepip --upgrade
                    "${PYTHON_PATH}" -m pip --version
                """
            }
        }
        stage('Install Dependencies') {
            steps {
                bat """
                    @echo off
                    "${PYTHON_PATH}" -m pip install --upgrade pip
                    "${PYTHON_PATH}" -m pip install -r requirements.txt
                """
            }
        }
        stage('Lint') {
            steps {
                bat """
                    @echo off
                    "${PYTHON_PATH}" -m flake8 app.py tests/
                """
            }
        }
        stage('Test') {
            steps {
                bat """
                    @echo off
                    "${PYTHON_PATH}" -m pytest --cov=app tests/ --cov-report=html
                """
            }
            post {
                always {
                    publishHTML(target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: false,
                        keepAll: true,
                        reportDir: 'htmlcov',
                        reportFiles: 'index.html',
                        reportName: 'Coverage Report'
                    ])
                }
            }
        }
        stage('Build') {
            steps {
                bat """
                    @echo off
                    "${PYTHON_PATH}" -m PyInstaller --onefile app.py
                """
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                bat """
                    @echo off
                    start "myflaskapp" /b "${PYTHON_PATH}" app.py
                """
            }
        }
    }
    post {
        success {
            echo 'CI/CD pipeline completed successfully!'
        }
        failure {
            echo 'CI/CD pipeline failed!'
        }
    }
}
