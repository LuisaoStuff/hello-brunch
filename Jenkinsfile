#!/usr/bin/env groovy
pipeline {
    agent any
    options {
        ansiColor('xterm')
    }    
    stages {
        stage('Build') {
            steps {
                sh 'docker-compose build'
            }
        }
        stage('Security') {
            steps {
                sh 'trivy fs --format json --output hello-brunch-fs-report.json .'
                sh 'trivy image --format json --output hello-brunch-report.json hello-brunch:1.0'
            }
            post {
                always {
                    recordIssues enabledForFailure: true, tool: trivy(pattern: '*.json')
                }
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker-compose up -d'
            }
        }
        
    }
}
