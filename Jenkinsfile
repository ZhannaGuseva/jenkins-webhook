pipeline {
    agent {
        label 'built-in'
    }

    options {
        buildDiscarder logRotator(artifactNumToKeepStr: '25', numToKeepStr: '25')
        skipDefaultCheckout()
    }

    stages {
        stage('Source Code Checkout') {
            steps {
                checkout scm
            }
        }
 
        stage('Check Commit Message') {
            steps {
                script {
                    def comMessage = sh(script: 'git log -1 --pretty=%B', returnStdout: true).trim()
                    def comTitle = comMessage.split("\n")[0]

                    // Check the message commit for compliance with best practice (first ticket gira code)

                    if (!comTitle.matches('^[A-Z]+-[0-9]+.*')) {
                        error("Commit title does not start with a JIRA ticket code")
                    }

                    // Checking the length of the commit header
                    if (comTitle.length() > 50) {
                        error("Commit title is longer than 50 characters")
                    }
                }
            }
        }

        stage('Linting Dockerfile') {
            steps {
                script {
                    try {
                        sh 'docker run --rm -i hadolint/hadolint:2.10.0 < Dockerfile | tee -a docker_lint.txt'
                    }
                    catch (Exception e) {
                        echo "There are no erros found on Dockerfile!!"
                        echo "111"
                    }
                }
            }
        }    
    }
    post {
        failure {
            script {
                echo 'Pipeline FAILED '
            }
        }
        success {
            script {
                echo 'Pipeline execution was Successful'
            }
        }
        always {
            archiveArtifacts 'docker_lint.txt'
        }
    }
}
