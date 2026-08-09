// El publish a npm se hace a mano (npm 2FA-bypass tokens dejan de servir para publish en CI, ver
// https://github.blog/changelog/2026-07-08-npm-install-time-security-and-gat-bypass2fa-deprecation/):
// `pnpm publish --no-git-checks` desde una terminal local autenticada.

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
