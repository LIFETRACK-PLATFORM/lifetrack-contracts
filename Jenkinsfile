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
        withCredentials([string(credentialsId: 'github-packages-token', variable: 'GITHUB_TOKEN')]) {
          script {
            def pkgVersion = sh(script: "node -p \"require('./package.json').version\"", returnStdout: true).trim()
            def alreadyPublished = sh(
              script: "npm view @lifetrack-platform/contracts@${pkgVersion} version --registry https://npm.pkg.github.com 2>/dev/null",
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
