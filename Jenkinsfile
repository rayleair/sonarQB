pipeline {
    agent any
    triggers {
        githubPush()
    }
    environment {
        SONARQUBE_URL = 'http://192.168.231.132:9000'
    }
    stages {
        stage('Checkout') {
            steps {
                script {
                    // Checkout the latest code from the repository
                    git url: 'https://github.com/rayleair/sonarQB.git', branch: 'main'
                }
            }
        }
        stage('Identify Changed Files') {
            steps {
                script {
                    // Get the list of changed files in the last commit
                    def changedFiles = sh(script: 'git diff-tree --no-commit-id --name-only -r HEAD', returnStdout: true).trim()
                    echo "Changed files: ${changedFiles}"
                }
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv('SonarQb') {
                        sh 'sonar-scanner -Dsonar.projectKey=my-php-project -Dsonar.sources=. -Dsonar.host.url=$SONARQUBE_URL -Dsonar.login=<votre_token_sonarqube>'
                    }
                }
            }
        }
        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    script {
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
        }
    }
    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'SonarQube analysis was successful'
        }
        failure {
            echo 'SonarQube analysis failed'
        }
    }
}
