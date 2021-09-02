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
          }
        }

        stage('Test app') {
          agent {
            docker {
              image 'gradle:6-jdk11'
            }
          }
          options{
            skipDefaultCheckout(true)
          }
          steps {
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }

      }
    }
    stage( 'push docker app'){
      environment {
        DOCKERCREDS = credentials('docker_login') //use the credentials just created in this stage
      }
      steps {
            unstash 'code' //unstash the repository code
            sh 'ci/build-docker.sh'
            sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin' //login to docker hub with the credentials above
            sh 'ci/push-docker.sh'
      }
    }
  }
}