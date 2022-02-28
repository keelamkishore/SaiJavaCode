pipeline {
  agent any
  tools {
  maven 'maven3.8'
  }
  environment {
    AWS_ACCOUNT_ID="071945506291"
    AWS_DEFAULT_REGION="ap-south-1"
    IMAGE_REPO_NAME="webapp_demo"
    IMAGE_TAG="latest"
    REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
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
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }

     // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
      steps{
        script {
          sh "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 071945506291.dkr.ecr.ap-south-1.amazonaws.com"
          sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
          sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }
    stage('Deploy to k8s') {
      steps {
        withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', serverUrl: '') {
          sh 'kubectl apply -f Deploy.yml'
        }
      }
    }
  }
}
