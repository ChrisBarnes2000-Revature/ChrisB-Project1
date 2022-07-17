pipeline {
  environment {
    registry = "chrisbarnes2000/project1"
    registrycredential = 'DOCKER_AUTH_ID'
    dockerimage = ''
    DOCKERHUB_CREDENTIALS=credentials('DOCKER_AUTH_ID')
  } // environment
  agent any
  stages {
    stage("Start Web Server") {
      steps {
        echo "Starting ... "
        sh "docker kill flask-container"
        sh "docker system prune -af"
        sh "docker build -t flask-image ."
        sh "docker run -d -p 5000:5000 --rm --name flask-container flask-image"
        echo "Please Visit --> $BASE_URL:5000"
      } // steps
    } // start
    stage('Deploy Image') {
      steps{
        script {
          sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
          sh 'docker push  ${registry}:${BUILD_NUMBER}'


          // def dockerImage = docker.build("${registry}:${BUILD_NUMBER}")
          // dockerImage.push()
          // sh "docker push ${registry}:${BUILD_NUMBER}"

          // docker.withRegistry( '', registryCredential ) {
          // } // withRegistry
        } // script
      } // steps
    } // deploy
    stage('Remove Unused Images') {
      steps{
        sh """
          docker kill ${docker ps -q}
          docker rm ${docker ps -a -q}
          docker rmi ${docker images -q}
          docker rmi ${registry}:${BUILD_NUMBER}
          docker system prune -af
        """
      } // steps
    } // Remove
  } // stages
  post {
    always {
      sh 'docker system prune -af && docker logout'

      mail to: "Chris.Barnes.2000@me.com",
           subject: "Job '${JOB_NAME}' (${BUILD_NUMBER}) Was A ${currentBuild.currentResult}",
           body: "Please go to ${BUILD_URL} and verify the build"

      discordSend webhookURL: "https://discord.com/api/webhooks/998320738769588224/4akFNyQItbFvUKmbGxJ-qMCyzMefF3QP4GbyNk73wry4_WfGPuDOWUlael_WN4_Yh677",
                  enableArtifactsList: false, scmWebUrl: "",
                  image: "", thumbnail: "",
                  title: JOB_NAME, link: BUILD_URL,
                  description: "Please Visit --> ${BASE_URL}:50000",
                  footer: "Jenkins Pipeline Build was a ${currentBuild.currentResult}",
                  result: currentBuild.currentResult
    } // always
  } // post
} // pipeline
