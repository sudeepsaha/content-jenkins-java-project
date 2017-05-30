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
       post {
          success {
            archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
          }
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
    stage("Running on Centos") {
      agent{
        label 'Slave1'
      }
      steps{
        sh "wget http://localhost/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 34 43"
      }
    }
  }
}
