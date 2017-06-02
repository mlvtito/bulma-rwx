#!/usr/bin/env groovy

node {
   def nodeHome
   stage('Preparation') {
       deleteDir()
      git 'https://backend.r-w-x.net/scm/git/bulma/bulma-rwx.git'
      nodeHome = tool 'NodeJS_4.6.0'
      env.PATH = "${nodeHome}/bin:${env.PATH}"
      sh "npm -v"
      sh "node -v"
      sh "jspm -v"
   }
   withEnv(["PATH=${tool 'NodeJS_4.6.0'}/bin:${PATH}"]) {
     stage('Install Dependencies') {
         sh "npm install"
     }
   }
}

stage('Build CSS') {
    node {
        withEnv(["PATH=${tool 'NodeJS_4.6.0'}/bin:${PATH}"]) {
            sh "npm run build"
        }
    }
}

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
