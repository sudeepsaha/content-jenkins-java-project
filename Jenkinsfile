pipeline {
  agent any

  environment {
    MAJOR_VERSION = 1
  }

  stages{
    stage('build') {
      steps {
        sh 'ant -f build.xml -v'
      }
    }
  }

  post {
    always {
      archive 'dist/*.jar'
    }
  }
}
