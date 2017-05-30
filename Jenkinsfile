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
        sh "mkdir -p /var/www/html/rectangles/all/${env.BRANCH_NAME}"
        sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
      }
    }
    stage('Running on Centos') {
      agent{
        label 'Slave1'
      }
      steps{
        sh "wget http://192.168.56.100/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 34 43"
      }
    }
    stage('Running on Debian distro'){
      agent {
        docker 'openjdk:8u121-jre'
      }
      steps {
        sh "wget http://192.168.56.100/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 39 93"
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
        sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
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
        echo "Refresh local"
        sh "git pull"
        echo "checkout master"
        sh "git checkout master"
        echo "Refresh local"
        sh "git pull"
        echo "merging develop to master"
        sh "git merge develop"
        echo "pushing to origin:master"
        sh "git push origin master"
        echo "Tagging release"
        sh "git tag rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
        echo "Push tag"
        sh "git push origin rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
      }
      post {
        success {
          emailext(
            subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] promoted to master!",
            body: "<p>Check console output ${env.BUILD_URL}</p>"
            to: "sudeep.ks1987@gmail.com"
          )
        }
      }
    }
  }
  post {
    failure {
      emailext(
        subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] failed!",
        body: "<p>Check console output ${env.BUILD_URL}</p>"
        to: "sudeep.ks1987@gmail.com"
      )
    }
  }
}
