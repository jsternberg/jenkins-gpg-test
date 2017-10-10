pipeline {
  agent any

  stages {
    stage("Sign repository files with GPG") {
      environment {
        GPG_PRIVATE_KEY_FILE = credentials("GPG_PRIVATE_KEY")
      }

      steps {
        sh """
        if [ ! -e private.key ]; then
          touch private.key
          chmod 600 private.key
        fi
        cat "${GPG_PRIVATE_KEY_FILE}" > private.key
        """

        withDockerContainer(image: "buildpack-deps:stretch-curl") {
          sh """
          set -e
          gpg --import private.key && rm -f private.key
          find . -type f | xargs -I{} gpg --armor --detach-sign {}
          """
        }
      }

      post {
        always {
          sh "rm -f private.key"
        }

        success {
          archiveArtifacts(artifacts: "*.asc")
        }
      }
    }
  }
}
