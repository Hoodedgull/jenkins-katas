pipeline {
  agent any
  stages {
    stage('Clone down'){
        agent any
        steps{
            
            stash excludes: '.git/', name: 'code'
            
        }
    }
    stage('Parallel Exec') {
      parallel {
        stage('Say Hello') {
          steps {
            sh 'echo "hello world"'
          }
        }

        stage('Build App') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          options {
              skipDefaultCheckout true
          }

          steps {
            unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
          }
        }
        stage('Test App') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          options {
              skipDefaultCheckout true
          }

          steps {
            unstash 'code'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }

      }
    }

  }
   post {
        always {

            deleteDir() /* clean up our workspace */
        }
}