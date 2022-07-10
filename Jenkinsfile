pipeline {
  agent any

  stages {
    stage("Verify Tools") {
      steps {
        sh '''
          git --version
          docker --version
          docker-compose --version
          python3 --version
          pip --version
        '''
      }
    }
    stage("Test") {
      when {
        expression {
          BRANCH_NAME == "test"
        }
      }
      steps {
        sh "python3 -m pytest app-test.py"
      }
    }
    stage("Start Web Server") {
      steps {
        echo "Starting ... "
        sh "docker system prune -af"
        sh "docker-compose up --build -d"
        echo "Please Visit --> $BASE_URL:5000"
      }
    }
    stage("Build & Push Image To Docker Hub") {
      steps {
        sh "docker-compose build --pull"
        sh "docker-compose push"
      } 
    }
  }

  post {
    always {
      sh "docker rmi $chrisbarnes2000/project1:$BUILD_NUMBER"
      sh "docker-compose down"
      sh "docker logout"

      discordSend webhookURL: "https://discord.com/api/webhooks/994018555341307966/V-Or2AnFnDNpfHa7slRrl2S0rhdybzYSnDNzKHVHgnKxJHCWG8iXWVQAPNjsa8hvHJ_q",
                  enableArtifactsList: false, scmWebUrl: "",
                  image: "", thumbnail: "",        
                  title: JOB_NAME, link: env.BUILD_URL,
                  description: "Please Visit --> ${JENKINS_URL}:5000",
                  footer: "Jenkins Pipeline Build was a ${currentBuild.currentResult}",
                  result: currentBuild.currentResult
    }
    success {
      mail to: "Chris.Barnes.2000@me.com",
      subject: "Job '${JOB_NAME}' (${BUILD_NUMBER}) Was A Success",
      body: "Please go to ${BUILD_URL} and verify the build"
    }
    failure {
      mail to: "Chris.Barnes.2000@me.com",
      subject: "Job '${JOB_NAME}' (${BUILD_NUMBER}) Failed",
      body: "Please go to ${BUILD_URL} and verify the build"
    }
  }
}
