#!/usr/bin/env groovy
pipeline {
    agent any
    options {
        ansiColor('xterm')
    }    
    stages {
        stage('Security') {
            steps {
                sh 'trivy fs --format json --output trivy-hello-brunch-fs-report.json .'
                sh 'trivy image --format json --output trivy-hello-brunch-report.json hello-brunch:1.0'
            }
            post {
                always {
                    recordIssues enabledForFailure: true, aggregatingResults:true, tool: trivy(pattern: 'trivy-*.json')
                }
            }
        }
        stage('Build') {
            steps {
                sh 'docker-compose build'
            }
        }
        stage('Publish'){
            steps{
                withDockerRegistry(credentialsId: 'gitlab-registry', url: 'http://10.250.5.20:5050') {
                    sh 'docker tag hello-brunch:latest 10.250.5.20:5050/luisaostuff/hello-brunch:BUILD-1.${BUILD_NUMBER}'
                    sh 'docker push 10.250.5.20:5050/luisaostuff/hello-brunch:BUILD-1.${BUILD_NUMBER}'
                    sh 'git tag BUILD-1.${BUILD_NUMBER}'
                    sh 'git remote add http://10.250.5.20:8929/LuisaoStuff/hello-brunch.git'
                    sh 'git push origin HEAD:container-registry-test'
                }
            }
        }
        
    }
}
