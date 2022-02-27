pipeline {
  agent any
  stages {
    stage('clone git repo') {
      steps {
        git 'https://github.com/keelamkishore/SaiJavaCode.git'
      }
    }
    stage('build with maven') {
      steps {
        sh 'mvn clean install package'
      }
    }
  }
}
