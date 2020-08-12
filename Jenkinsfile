pipeline {
  agent any
  stages {
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
          steps {
            sh 'ci/build-app.sh'
          }
        }

      }
    }

    stage('Archive artefacts') {
      steps {
        archiveArtifacts 'app/build/libs/'
      }
    }

  }
}