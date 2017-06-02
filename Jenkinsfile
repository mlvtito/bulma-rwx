#!/usr/bin/env groovy

withEnv(["PATH=${tool 'NodeJS_4.6.0'}/bin:${PATH}"]) {
  stage('Preparation') {
    node {
      deleteDir()
      git 'https://backend.r-w-x.net/scm/git/bulma/bulma-rwx.git'
      sh "node -v && npm -v"
    }
  }

  stage('Install Dependencies') {
    node {
      sh "npm install"
    }
  }

  stage('Build CSS') {
    node {
      sh "npm run build"
    }
  }

  stage('Publish to NPM') {
    timeout(time:5, unit:'DAYS') {
       input 'Should we deliver this version ?'
    }
    node {
      sh "npm publish"
    }
  }
}
