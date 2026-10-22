
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                bat '''
                    @echo off
                    set "PYTHON_CMD="

                    where python >nul 2>&1
                    if %ERRORLEVEL% EQU 0 set "PYTHON_CMD=python"

                    if not defined PYTHON_CMD (
                        if exist "%LOCALAPPDATA%\\Programs\\Python\\Python313\\python.exe" (
                            set "PYTHON_CMD=%LOCALAPPDATA%\\Programs\\Python\\Python313\\python.exe"
                        )
                    )

                    if not defined PYTHON_CMD (
                        if exist "C:\\Users\\HPLAP640G459789\\AppData\\Local\\Programs\\Python\\Python313\\python.exe" (
                            set "PYTHON_CMD=C:\\Users\\HPLAP640G459789\\AppData\\Local\\Programs\\Python\\Python313\\python.exe"
                        )
                    )

                    if not defined PYTHON_CMD (
                        echo ERROR: Python was not found.
                        exit /b 1
                    )

                    echo Python executable: %PYTHON_CMD%
                    "%PYTHON_CMD%" --version

                    if not exist ".venv\\Scripts\\python.exe" (
                        "%PYTHON_CMD%" -m venv .venv
                        if errorlevel 1 exit /b 1
                    )

                    .venv\\Scripts\\python.exe -m pip install --upgrade pip
                    if errorlevel 1 exit /b 1

                    .venv\\Scripts\\python.exe -m pip install -r requirements.txt
                    if errorlevel 1 exit /b 1
                '''
            }
        }

        stage('Test') {
            steps {
                bat '''
                    .venv\\Scripts\\python.exe -m pytest -v
                '''
            }
        }

        stage('Run Application') {
            steps {
                bat '''
                    .venv\\Scripts\\python.exe app.py
                '''
            }
        }

        stage('Package Release') {
            steps {
                bat '''
                    if not exist release mkdir release
                    copy app.py release\\app.py
                    copy requirements.txt release\\requirements.txt
                    copy Jenkinsfile release\\Jenkinsfile
                '''
            }
            post {
                success {
                    archiveArtifacts artifacts: 'release/**', fingerprint: true
                }
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: All pipeline stages completed!'
        }
        failure {
            echo 'FAILED: Check Console Output for the error.'
        }
    }
}

