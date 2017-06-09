#!/usr/bin/env groovy
node {
  withEnv(["PATH=${tool 'NodeJS_4.6.0'}/bin:${PATH}"]) {
    stage('Preparation') {
      deleteDir()
      git 'ssh://ci@91.121.149.68:29418/bulma/bulma-rwx.git'
      sh "node -v && npm -v"
    }

    stage('Install Dependencies') {
      sh "npm install"
    }

    stage('Build CSS') {
      sh "npm run build"
      step([$class: 'ArtifactArchiver', artifacts: 'css/*', fingerprint: true])
      step([$class: 'ArtifactArchiver', artifacts: 'dist/*', fingerprint: true])
      lastPublishedVersion = sh(script: 'npm view bulma-rwx version', returnStdout: true).trim()
      currentVersion = sh(script: 'npm version | grep bulma-rwx | cut -d "\'" -f 4', returnStdout: true).trim()
    }
  }
}

if( lastPublishedVersion != currentVersion ) {
stage('Publish to NPM') {
  timeout(time:5, unit:'DAYS') {
    input 'Should we deliver this version ?'
  }
  node {
    withEnv(["PATH=${tool 'NodeJS_4.6.0'}/bin:${PATH}"]) {
      sh "npm publish"
    }
  }
}
}

