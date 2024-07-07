pipeline {
    agent any
    triggers {
        githubPush()
    }
    environment {
        SONARQUBE_URL = 'http://192.168.231.132:9000'
        SONARQUBE_SCANNER = '/opt/sonar-scanner/bin/sonar-scanner'
    }
    stages {
        stage('Checkout') {
            steps {
                script {
                    git url: 'https://github.com/rayleair/sonarQB.git', branch: 'main'
                }
            }
        }
        stage('Identify Changed Files') {
            steps {
                script {
                    def changedFiles = sh(script: 'git diff-tree --no-commit-id --name-only -r HEAD', returnStdout: true).trim()
                    echo "Changed files: ${changedFiles}"
                }
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv('SonarQb') {
                        sh "${env.SONARQUBE_SCANNER} -Dsonar.projectKey=my-php-project -Dsonar.sources=. -Dsonar.host.url=$SONARQUBE_URL -Dsonar.login=sqp_9dae9af6266f850890e0eab631d0ea053eff2d59"
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

