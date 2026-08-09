pipeline {
  agent any

  tools {
    nodejs "NodeJS-20"
  }

  stages {
    stage("Install") {
      steps {
        sh "npm install -g pnpm@10.21.0"
        sh "pnpm install --frozen-lockfile"
      }
    }

    stage("Publish") {
      steps {
        withCredentials([string(credentialsId: 'npm-publish-token', variable: 'NPM_TOKEN')]) {
          script {
            sh "npm config set //registry.npmjs.org/:_authToken=${NPM_TOKEN}"

            def pkgVersion = sh(script: "node -p \"require('./package.json').version\"", returnStdout: true).trim()
            def alreadyPublished = sh(
              script: "npm view @lifetrack/contracts@${pkgVersion} version 2>/dev/null",
              returnStatus: true
            ) == 0

            if (alreadyPublished) {
              echo "contracts@${pkgVersion} ya está publicada, no hay nada que hacer."
            } else {
              sh "pnpm publish --no-git-checks"
            }
          }
        }
      }
    }
  }

  post {
    success {
      echo "Pipeline OK - contracts #${env.BUILD_NUMBER}"
    }
    failure {
      echo "Pipeline FAILED - contracts #${env.BUILD_NUMBER}"
    }
  }
}
