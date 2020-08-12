pipeline {
  agent any
  environment {
  docker_username = "hoodedgull"
    }

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
            stash includes: 'app/', name: 'jarfile'
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
    
        stage('Component Test') {
          when {
  not {
    branch 'dev/*'
  }
}
          
          options {
              skipDefaultCheckout true
          }

          steps {
            unstash 'code'
            sh 'ci/component-test.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }

    
    
    
    
    stage('Push docker app'){
        when {branch "master"}
    environment {
      DOCKERCREDS = credentials('docker_login') //use the credentials just created in this stage
    }
    steps {
      unstash 'jarfile' //unstash the repository code
      sh 'ci/build-docker.sh'
      sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin' //login to docker hub with the credentials above
      sh 'ci/push-docker.sh'
    }
    }

  }
   post {
        always {

            deleteDir() /* clean up our workspace */
        }
   }
}