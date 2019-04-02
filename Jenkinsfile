#!/usr/bin/env groovy
pipeline { 
  agent { 
    node { 
      label 'docker'
    }
  }
  tools {
    nodejs 'nodejs'
  }
 
  stages {
    stage ('Checkout Code') {
      steps {
        checkout scm
      }
    }
    stage ('Verify Tools'){
      steps {
        parallel (
          node: { sh "npm -v" },
          docker: { sh "docker -v" }
        )
      }
    }
    stage ('Build app') {
      steps {
        sh "npm prune"
        sh "npm install"
      }
    }
    stage ('Test'){
      steps {
        sh "npm test"
      }
    }
 
    stage ('Build container') {
      steps {
        sh "docker build -t depoasem/nodelocal:latest ."
        sh "docker tag depoasem/nodelocal:latest depoasem/nodelocal:v${env.BUILD_ID}"
      }
    }
    stage ('Deploy') {
      steps {
        input "Ready to deploy?"
        sh "docker stack rm nodelocal"
        sh "docker stack deploy nnodelocal --compose-file docker-compose.yml"
        sh "docker service update nodelocal_server --image bdepoasem/nodelocal:v${env.BUILD_ID}"
      }
    }
    stage ('Verify') {
      steps {
        input "Everything good?"
      }
    }
    stage ('Clean') {
      steps {
        sh "npm prune"
        sh "rm -rf node_modules"
      }
    }
  }
}