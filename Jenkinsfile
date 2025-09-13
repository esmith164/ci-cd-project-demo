// Groovy vars (safe names, quoted strings)
def dockerCredentialsName = 'dockerhub-creds'
def imageName            = 'esmith164/demoapplication'
def dockerImage          = null

pipeline {
  agent any
  tools { nodejs 'node' }

  stages {
    stage('Environment') {
      steps {
        echo "Branch: ${env.BRANCH_NAME}"
        sh 'docker -v'
      }
    }

    stage('Install dependencies') {
      steps {
        // faster, reproducible installs (use npm install if you prefer)
        sh 'npm ci || npm install'
      }
    }

    stage('Test') {
      steps {
        sh 'npm test'
      }
    }

    stage('Build image') {
      steps {
        script {
          dockerImage = docker.build(imageName)
        }
      }
    }

    stage('Deploy Image') {
      // optional: only push from main; adjust as needed
      // when { branch 'main' }
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', dockerCredentialsName) {
            dockerImage.push("${env.BUILD_NUMBER}")
            dockerImage.push("latest")
          }
        }
      }
    }
  }

  post {
    always {
      // keep your node clean; ignore errors if no images
      sh 'docker image prune -f || true'
    }
  }
}
