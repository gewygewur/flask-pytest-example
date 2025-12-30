pipeline {
  agent any

  options { timestamps() }

  stages {
    stage('Clone Repository') {
      steps {
        checkout scm
      }
    }

    stage('Install Dependencies') {
      steps {
        sh '''
          python3 --version
          python3 -m venv .venv
          . .venv/bin/activate
          python -m pip install --upgrade pip
          pip install -r requirements.txt
        '''
      }
    }

    stage('Run Unit Tests (pytest)') {
      steps {
        sh '''
          . .venv/bin/activate
          pytest -q
        '''
      }
    }

    stage('Build Application') {
      steps {
        sh '''
          rm -rf build
          mkdir -p build
          # Simple “build”: copy source files into build/ as a deployable bundle
          cp -r app.py __init__.py handlers tests requirements.txt build/
          echo "Build contents:"
          ls -la build
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
        sh '''
          rm -rf deploy_target
          mkdir -p deploy_target
          cp -r build/* deploy_target/
          echo "Deployed to workspace/deploy_target:"
          ls -la deploy_target
        '''
      }
    }
  }
}
