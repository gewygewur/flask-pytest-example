pipeline {
  agent any
  options { timestamps() }

  stages {
    stage('Clone Repository') {
      steps { checkout scm }
    }

    stage('Install Dependencies') {
      steps {
        bat '''
          python --version
          python -m venv .venv
          call .venv\\Scripts\\activate
          python -m pip install --upgrade pip
          pip install -r requirements.txt
        '''
      }
    }

    stage('Run Unit Tests (pytest)') {
      steps {
        bat '''
          call .venv\\Scripts\\activate
          pytest -q
        '''
      }
    }

    stage('Build Application') {
      steps {
        bat '''
          if exist build rmdir /s /q build
          mkdir build

          copy /Y app.py build\\
          copy /Y requirements.txt build\\
          xcopy /E /I /Y tests build\\tests\\

          echo Build contents:
          dir build
        '''
      }
      post {
        success {
          archiveArtifacts artifacts: 'build/**', fingerprint: true
        }
      }
    }

    stage('Deploy Application (Simulated)') {
      steps {
        bat '''
          if exist deploy_target rmdir /s /q deploy_target
          mkdir deploy_target
          xcopy /E /I /Y build deploy_target

          echo Deployed to workspace\\deploy_target:
          dir deploy_target
        '''
      }
    }
  }
}
