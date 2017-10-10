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
          gpg --import private.key
          for file in *; do
            gpg --armor --detach-sign "$file"
          done
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
