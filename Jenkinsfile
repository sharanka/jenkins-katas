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
          }
        }

      }
    }

  }
}