pipeline {
    agent any
    environment {
        // Environment variables from Jenkins job configuration
        CI = 'true'
        ANGULAR_ENV = "development" // For example: 'production' or 'development'
        EC2_IP = "13.127.93.33"
        APP_DIR = "${APP_DIR}"
    }
    stages {
        stage('Build') {
            steps {
                script {
                    // Install Node.js and Angular CLI if not already installed
                    sh 'npm install -g @angular/cli'
                }
                sh 'npm install' // Install project dependencies
                sh 'ng build --configuration=${ANGULAR_ENV}' // Build Angular app
                archiveArtifacts artifacts: 'dist/**/*', allowEmptyArchive: true // Archive build artifacts
            }
        }
        stage('Test') {
            steps {
                sh 'ng test --watch=false' // Run tests
                archiveArtifacts artifacts: 'test-results/**/*', allowEmptyArchive: true // Archive test results
            }
        }
        stage('Deploy') {
            steps {
                sshagent(credentials: ['ec2-ssh-credentials']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ec2-user@${EC2_IP} << EOF
                        cd ${APP_DIR}
                        rm -rf * // Remove old files
                        git pull // Update code from repository
                        mkdir dist // Create directory for new build files
                        exit
                    EOF
                    scp -o StrictHostKeyChecking=no -r dist/* ec2-user@${EC2_IP}:${APP_DIR}/dist/ // Copy new build files
                    """
                }
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
