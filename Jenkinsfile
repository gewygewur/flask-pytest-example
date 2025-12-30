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
