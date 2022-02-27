pipeline {
  agent any
  tools {
  maven 'maven3.8'
  }
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
    stage('upload artifact to s3') {
      steps {
        s3Upload consoleLogLevel: 'INFO', dontSetBuildResultOnFailure: false, dontWaitForConcurrentBuildCompletion: false, entries: [[bucket: 'artifact-upload-to-s3', excludedFile: '', flatten: false, gzipFiles: false, keepForever: false, managedArtifacts: false, noUploadOnFailure: false, selectedRegion: 'ap-south-1', showDirectlyInBrowser: false, sourceFile: '**/target/*.war', storageClass: 'STANDARD', uploadFromSlave: false, useServerSideEncryption: false]], pluginFailureResultConstraint: 'FAILURE', profileName: 's3_artifact_upload', userMetadata: []
      }
    }
    stage('build docker image') {
      steps {
        withDockerRegistry(url: 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 071945506291.dkr.ecr.ap-south-1.amazonaws.com') {
        }
        sh 'docker build -t webapp_demo .'
        docker tag webapp_demo:latest 071945506291.dkr.ecr.ap-south-1.amazonaws.com/webapp_demo:latest
        docker push 071945506291.dkr.ecr.ap-south-1.amazonaws.com/webapp_demo:latest
      }
    }
  }
}
