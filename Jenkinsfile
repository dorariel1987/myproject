pipeline {
  agent none
}
stage {
  stage('Build') {
    agent none
    enviromnet { 
      LOG_LEVEL='INFO'
    
    }
    steps {
      echo "Building release ${RELEASE} with log level ${LOG_LEVEL}..."
    }
  }
  stage('TEST') {
    steps {
      echo "Testing. I can see release ${RELEASE} , but not log level ${LOG_LEVEL}"
    }
  }
}
