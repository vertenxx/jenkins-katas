pipeline {
  agent any
  stages {
    stage('Parallel execution') {
      parallel {
        stage( 'clone down'){
          agent{
            node{
              label "swarm"
            }
          } 
          steps{
            stash(name: "code", excludes ".git")
          }
        }

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
          skipDefaultCheckout(true)
          steps {
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
          }
        }

      }
    }

  }
}