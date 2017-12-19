#!/usr/bin/env groovy

pipeline {
    agent any
    stages {
        stage('Preparation') {
            tools {
                nodejs 'NodeJS_8.6.0'
            }
            steps {
                deleteDir()
                git 'ssh://ci@91.121.149.68:29418/bulma/bulma-rwx.git'
                sh "node -v && npm -v"
            }
        }
        stage('Install Dependencies') {
            tools {
                nodejs 'NodeJS_8.6.0'
            }
            steps {
                sh "npm install"
            }
        }
        stage('Build CSS') {
            tools {
                nodejs 'NodeJS_8.6.0'
            }
            steps {
                sh "npm run build"
                step([$class: 'ArtifactArchiver', artifacts: 'css/*', fingerprint: true])
                step([$class: 'ArtifactArchiver', artifacts: 'dist/*', fingerprint: true])
                env.lastPublishedVersion = sh(script: 'npm view bulma-rwx version', returnStdout: true).trim()
                env.currentVersion = sh(script: 'npm version | grep bulma-rwx | cut -d "\'" -f 4', returnStdout: true).trim()
            }
        }

        stage('Publish to NPM') {
            tools {
                nodejs 'NodeJS_8.6.0'
            }
            when {
                expression {
                    return env.lastPublishedVersion != env.currentVersion;
                }
            }
            steps {
                timeout(time:5, unit:'DAYS') {
                    input 'Should we deliver this version ?'
                }
                sh "npm publish"
            }
        }

        stage('Publish Site') {
            when {
                expression {
                    return env.lastPublishedVersion != env.currentVersion;
                }
            }
            steps {
                sh "scp dist/bulma-rwx-site-"+currentVersion+".zip rwxywdhy@ftp.r-w-x.net:/homez.32/rwxywdhy/depot/bulma-rwx/"
                sh 'ssh rwxywdhy@ftp.r-w-x.net "mkdir -p /homez.32/rwxywdhy/bulma-rwx/'+currentVersion+'"'
                sh 'ssh rwxywdhy@ftp.r-w-x.net "unzip -o /homez.32/rwxywdhy/depot/bulma-rwx/bulma-rwx-site-'+currentVersion+'.zip -d /homez.32/rwxywdhy/bulma-rwx/'+currentVersion+'/"'
                sh 'ssh rwxywdhy@ftp.r-w-x.net "rm -f /homez.32/rwxywdhy/bulma-rwx/current && ln -sf /homez.32/rwxywdhy/bulma-rwx/'+currentVersion+' /homez.32/rwxywdhy/bulma-rwx/current"'
                sh 'ssh rwxywdhy@ftp.r-w-x.net "find /homez.32/rwxywdhy/bulma-rwx -type f"'
            }
        }

        stage('Tag Version') {
            when {
                expression {
                    return env.lastPublishedVersion != env.currentVersion;
                }
            }
            steps {
                sh "git tag " + currentVersion
                sh "git push --tags"
            }
        }
    }
}


