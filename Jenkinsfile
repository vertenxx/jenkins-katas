pipeline {
  agent any
  stages {
    stage( 'Clone down'){
          agent { label "swarm" }
          steps{
            stash(name: "code", excludes: ".git")
          }
        }

    stage('Parallel execution') {
      parallel {
        stage('Say Hello') {
          steps {
            sh '''echo "hello world"
'''
          }
        }

        stage('Build app') {
          agent {
            docker {
              image 'gradle:6-jdk11'
            }
          }
          options{
            skipDefaultCheckout(true)
          }
          steps {
            unstash "code"
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }

      }
    }

  }
}