pipeline {
  agent none

  environment {
    MAJOR_VERSION = 1
  }

  stages{
    stage('Unit Tests') {
     agent {
        label 'apache'
      }
      steps {
              sh 'ant -f test.xml -v'
              junit 'reports/result.xml'
            }
    }
    stage('build') {
      agent {
        label 'apache'
      }
      steps {
        sh 'ant -f build.xml -v'
      }
    }
    stage('deploy')    {
      agent {
        label 'apache'
      }
      steps {
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
      }
    }
  }

  post {
    success {
     agent {
        label 'apache'
      }
      archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
    }
  }
}
