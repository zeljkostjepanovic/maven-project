pipeline {
  /* Declarative pipeline */
    agent any

    triggers {
      pollSCM('H/2 * * * 1-5')
    }
    stages {
        stage('Build') {
            options {
              buildDiscarder(logRotator(numToKeepStr: '1'))
            }
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
            post {
              success {
                echo 'Now Archiving...'
                archiveArtifacts artifacts: '**/target/*.war'
              }
            }
        }
        stage('Deploy to Staging') {
          steps {
            build job: 'deploy-to-staging'
          }
        }
        stage('Deploy to Production') {
          steps {
            timeout(time:5, unit:'DAYS'){
              input message: 'Approve PRODUCTION Deployment?'
            }
            build job: 'deploy-to-prod'
          }
          post {
            success {
              echo 'Code deployed to Production.'
            }
            failure {
              echo 'Deployment failed.'
            }
            always {
              cleanWs()
            }
          }
        }
    }
}
