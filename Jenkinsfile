node {
    deleteDir()
    checkout scm

    stage('build') {
        docker.image('python:3.7').inside {
            sh 'pip install -r requirements.txt'
            sh 'mkdocs build'
            archiveArtifacts "site"
        }
    }

    if(env.BRANCH == 'master') {
        stage('publish') {
            sh 'mkdir build/gh-pages'
            dir('build/gh-pages') {
                git branch: 'gh-pages', changelog: false, credentialsId: 'jenkins-drunkenoctop.us', poll: false, url: 'git@github.com:drunken-octopus/drunkenoctop.us.git'
                sh 'rsync --exclude ".git" -avz --delete ../site/* .'
                sh 'git config user.email "jenkins+drunken-octopus@custenborder.com"'
                sh 'git config user.name "Jenkins"'
                sh "echo `git add --all . && git commit -m 'Build ${BUILD_NUMBER}' .`"
                sshagent(credentials: ['jenkins-drunkenoctop.us']) {
                    sh "git push 'git@github.com:drunken-octopus/drunkenoctop.us.git' gh-pages"
                }
            }
        }
    }
}