pipeline {
    agent any
    environment {
        // Environment variables from Jenkins job configuration
        CI = 'true'
        ANGULAR_ENV = "development" // For example: 'production' or 'development'
    }
    stages {
        stage('Build') {
            steps {
                // Install Angular CLI locally
                sh 'npm install @angular/cli'
            }
        }
        stage('Test') {
            steps {
                echo 'test' // Run tests
                archiveArtifacts artifacts: 'test-results/**/*', allowEmptyArchive: true // Archive test results
            }
        }
        stage('Post-Deployment') {
            steps {
                // Archive deployment logs or any other relevant files
                archiveArtifacts artifacts: 'deploy-logs/**/*', allowEmptyArchive: true
            }
        }
    }
    post {
        always {
            cleanWs() // Clean up the workspace after the build
        }
    }
}
