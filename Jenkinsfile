// Jenkinsfile for Repo A (Application Code) CI
// This pipeline:
// 1) Checks out the code
// 2) Builds a Docker image
// 3) Tags the image with a version based on the Jenkins build number
// 4) Pushes the tagged image to Docker Hub

pipeline {
  agent any

  // Define variables used across stages
  environment {
    // Update these 2 values to match your Docker Hub:
    // - DOCKERHUB_IMAGE_REPO must be "username/repo-name" in lowercase.
    DOCKERHUB_IMAGE_REPO = "fyooie11/devopsassignment2fastapi-dockerfile"

    // Version tag requirement: use a version number that corresponds to the Jenkins build.
    // Example format: v1.0.123 where 123 = Jenkins BUILD_NUMBER
    VERSION_TAG = "v1.0.${env.BUILD_NUMBER}"

    // Local (temporary) image tag for the build step
    LOCAL_IMAGE_TAG = "fastapi-app:${env.BUILD_NUMBER}"
  }

  stages {
    stage('Get Code') {
      steps {
        // For Pipeline-as-Code from SCM, Jenkins provides `scm`.
        // This checks out the current branch/commit being built.
        checkout scm
      }
    }

    stage('Build Docker Image') {
      steps {
        // Build the Docker image using the Dockerfile in the repo root.
        sh "docker build -t ${LOCAL_IMAGE_TAG} ."
      }
    }

    stage('Tag Images') {
      steps {
        // Apply the required Docker Hub tag (version-based, not only "latest").
        sh "docker tag ${LOCAL_IMAGE_TAG} ${DOCKERHUB_IMAGE_REPO}:${VERSION_TAG}"
      }
    }

    stage('Push to Docker Hub') {
      steps {
        // Pull Docker Hub credentials from Jenkins Credentials.
        // Change `dockerhub-credentials` to the credentialsId you create in Jenkins.
        withCredentials([
          usernamePassword(
            credentialsId: 'dockerhub-credentials',
            usernameVariable: 'DOCKERHUB_USER',
            passwordVariable: 'DOCKERHUB_PASS'
          )
        ]) {
          // Login without exposing the password in logs.
          sh 'echo $DOCKERHUB_PASS | docker login -u $DOCKERHUB_USER --password-stdin'

          // Push the version-tagged image to Docker Hub.
          sh "docker push ${DOCKERHUB_IMAGE_REPO}:${VERSION_TAG}"
        }
      }
    }
  }
}

