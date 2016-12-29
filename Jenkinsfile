node {

  stage("Clone repo") {
    checkout scm
  }

  stage("Build docker slave") {
    docker.build('sawtooth-build:$BUILD_TAG', '-f core/sawtooth/cli/data/sawtooth-build-ubuntu-xenial .')
  }

  stage("Build the packages"){
    docker.withServer('tcp://0.0.0.0:4243'){
      docker.image('sawtooth-build:$BUILD_TAG').inside {
        sh './bin/build_debs'
        stash name: 'debs', includes: 'core/deb_dist/*.deb,signing/deb_dist/*.deb,*.deb'
      }
    }
  }

  stage("Docker compose") {
    dir("ci") {
      unstash name: 'debs'
      sh 'docker-compose up'
    }
  }

  stage("Remove docker image") {
    sh 'docker rmi sawtooth-build:$BUILD_TAG'
  }
}
