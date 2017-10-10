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
        cat private.key
        """

        withDockerContainer(image: "jsternberg/jenkins-gpg-signer") {
          sh """
          gpg --import private.key
          echo \$?
          gpg --list-secret-keys
          rm -f private.key
          echo \$?
          find . -type f
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
