pipeline {
  agent any
  stages {
    stage('Say hello') {
      parallel {
        stage('Parallel execution') {
          steps {
            sh 'echo "Hello World"'
          }
        }

        stage('Build app') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
          }
        }

        stage('Test app') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
            sh 'ci/unit-test-app.sh'
            archiveArtifacts 'junit \'app/build/test-results/test/TEST-*.xml'
          }
        }

      }
    }

  }
}