pipeline {
  agent any

  stages {
    stage("Sign repository files with GPG") {
      environment {
        GPG_PRIVATE_KEY_FILE = credentials("GPG_PRIVATE_KEY")
      }

      steps {
        withDockerContainer(image: "buildpack-deps:stretch-curl") {
          sh """
          gpg --import "${GPG_PRIVATE_KEY_FILE}"
          for file in *; do
            gpg --armor --detach-sign "$file"
          done
          """
        }
      }

      post {
        success {
          archiveArtifacts(artifacts: "*.asc")
        }
      }
    }
  }
}
