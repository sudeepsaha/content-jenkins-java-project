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
        sh "mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}"
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
      }
    }
    stage('Running on Centos') {
      agent{
        label 'Slave1'
      }
      steps{
        sh "wget http://192.168.56.100/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 34 43"
      }
    }
    stage('Running on Debian distro'){
      agent {
        docker 'openjdk:8u121-jre'
      }
      steps {
        sh "wget http://192.168.56.100/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 39 93"
      }
    }
    stage('Promote to green') {
      agent{
        label 'apache'
      }
      when {
        branch 'master'
      }
      steps {
        sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.BUILD_NUMBER}.jar"
      }
    }
    stage('Promote develop to master') {
      agent{
        label 'apache'
      }
      when {
        branch 'develop'
      }
      steps {
        echo "Stash any local changes"
        sh "git stash"
        echo "Checking out develop"
        sh "git checkout develop"
        echo "checkout master"
        sh "git checkout master"
        echo "merging develop to master"
        sh "git merge develop"
        echo "pushing to origin:master"
        sh "git push origin master"
      }
    }
  }
}
