pipeline {
  agent any

  options { timestamps() }

  environment {
    VENV_DIR   = ".venv"
    BUILD_DIR  = "build"
    DEPLOY_DIR = "deploy_target"
  }

  stages {

    stage('Clone Repository') {
      steps {
        checkout scm
      }
    }

    stage('Install Dependencies') {
      steps {
        bat """
          python --version

          if exist %VENV_DIR% rmdir /s /q %VENV_DIR%
          python -m venv %VENV_DIR%
          call %VENV_DIR%\\Scripts\\activate

          python -m pip install --upgrade pip
          pip install -r requirements.txt

          REM Install THIS repository as a package so tests can import flask_pytest_example
          pip install -e .
        """
      }
    }

    stage('Run Unit Tests (pytest)') {
      steps {
        bat """
          call %VENV_DIR%\\Scripts\\activate
          pytest -q --junitxml=pytest-report.xml
          exit /b %ERRORLEVEL%
        """
      }
      post {
        always {
          junit 'pytest-report.xml'
        }
      }
    }

    stage('Build Application') {
      steps {
        bat """
          if exist %BUILD_DIR% rmdir /s /q %BUILD_DIR%
          mkdir %BUILD_DIR%

          REM Copy core files
          if exist app.py copy /Y app.py %BUILD_DIR%\\
          if exist requirements.txt copy /Y requirements.txt %BUILD_DIR%\\
          if exist setup.py copy /Y setup.py %BUILD_DIR%\\
          if exist __init__.py copy /Y __init__.py %BUILD_DIR%\\

          REM Copy package folder (the important one)
          if exist flask_pytest_example (
            xcopy flask_pytest_example %BUILD_DIR%\\flask_pytest_example\\ /E /I /Y
          )

          REM Copy tests (optional)
          if exist tests (
            xcopy tests %BUILD_DIR%\\tests\\ /E /I /Y
          )

          echo ==== Build contents ====
          dir %BUILD_DIR%
        """
      }
      post {
        success {
          archiveArtifacts artifacts: 'build/**', fingerprint: true
        }
      }
    }

    stage('Deploy Application (Simulated)') {
      steps {
        bat """
          if exist %DEPLOY_DIR% rmdir /s /q %DEPLOY_DIR%
          mkdir %DEPLOY_DIR%

          xcopy %BUILD_DIR% %DEPLOY_DIR%\\ /E /I /Y

          echo ==== Deployed to workspace\\%DEPLOY_DIR% ====
          dir %DEPLOY_DIR%
        """
      }
    }
  }

  post {
    always {
      echo "Pipeline finished."
    }
  }
}
