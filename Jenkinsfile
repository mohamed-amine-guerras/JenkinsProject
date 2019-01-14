pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        bat 'gradle build'
        bat 'gradle javadoc'
        bat 'echo \'he\''
        archiveArtifacts 'build/libs/*jar'
      }
    }
   post {
      success {
          mail(subject: 'Success', body: "Successful build", from: 'jenkins-notification@jenkins.com', to: 'sissouma12@gmail.com')
      }
      failure {
          mail(subject: "Failed", body: "Failed build", from: 'jenkins-notification@jenkins.com', to: 'sissouma12@gmail.com')
      }
    }
    stage('Mail Notification') {
      steps {
        bat 'echo \'hi\''
      }
    }
    stage('Code Analysis') {
      parallel {
        stage('Sonarqube') {
          environment {
            scannerHome = tool 'SonarQubeScanner'
          }
          steps {
            withSonarQubeEnv('sonarqube') {
              bat "${scannerHome}/sonar-scanner"
            }

            timeout(time: 10, unit: 'MINUTES') {
              waitForQualityGate true
            }

          }
        }
        stage('Test Reporting') {
          steps {
            bat 'gradle jacocoTestReport'
          }
        }
      }
    }
    stage('Deployment') {
      steps {
        bat 'gradle uploadArchives'
      }
    }
    stage('Slack Notification') {
      steps {
        slackSend(baseUrl: 'https://gradlesil.slack.com/services/hooks/jenkins-ci/', channel: '#jenkins', message: 'Build successful')
      }
    }
  }
}
