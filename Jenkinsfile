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
    stage('Running on Centos') {
      agent{
        label 'Slave1'
      }
      steps{
        sh "wget http://192.168.56.100/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 34 43"
      }
    }
    stage('Running on Debian distro'){
      agent {
        docker 'openjdk:8u121-jre'
      }
      steps {
        sh "wget http://192.168.56.100/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 39 93"
      }
    }
    stage('Promote to green') {
      agent{
        label 'apache'
      }
      steps {
        sh "cp /var/www/html/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.BUILD_NUMBER}.jar"
      }
    }
  }
}
