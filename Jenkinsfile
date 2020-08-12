pipeline {
  agent any
  stages {
    stage('_clone down_') {
      steps {
        stash(excludes: '.git', name: 'code')
      }
    }

    stage('Parallel Execution') {
      parallel {
        stage('Say Hello') {
          steps {
            sh '''echo "Hello, World!"
'''
          }
        }

        stage('Build App') {
          agent {
            docker {
              image 'gradle:jdk11'
            }
          }
          steps {
            unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            stash(excludes: '.git', name: 'build')
            sh 'ls -a'
            deleteDir()
            sh 'ls -a'
            skipDefaultCheckout true
          }
        }

        stage('Test App') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
            unstash 'code'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }
      }

    }
    stage('Build docker app') {
      environment {
        docker_username = 'sharanka'
        DOCKERCREDS = credentials('docker_login') //use the credentials just created in this stage
      }
      steps {
            unstash 'build'  //unstash the repository code
            sh 'ci/build-docker.sh'
          }
    }

    stage('Push docker app') {
      when { branch 'master' }
      environment {
        docker_username = 'sharanka'
        DOCKERCREDS = credentials('docker_login') //use the credentials just created in this stage
      }
      steps {
        unstash 'build'  //unstash the repository code
        sh 'ci/build-docker.sh'
        sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin' //login to docker hub with the credentials above
        sh 'ci/push-docker.sh'
        }
    }
    stage('Componet test') {
      when { not { branch pattern:'dev/*' } || changeRequest()}
      environment {
        docker_username = 'sharanka'
        DOCKERCREDS = credentials('docker_login') //use the credentials just created in this stage
      }
      steps {
        unstash 'build'
        sh 'ci/component-test.sh'
        
      }

    }
  }
}