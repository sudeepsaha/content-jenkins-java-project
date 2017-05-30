pipeline {
  agent {
    label 'master'
  }

  environment {
    MAJOR_VERSION = 1
  }

  stages{
     stage('Unit Tests') {
      steps {
              sh 'ant -f test.xml -v'
              junit 'reports/result.xml'
            }
    }
    stage('build') {
      steps {
        sh 'ant -f build.xml -v'
      }
    }
    stage('deploy')    {
      steps {
        sh 'cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/htmlm/rectangles'
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
    }
  }
}
