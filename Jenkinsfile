pipeline {
    agent any
    triggers {
        githubPush()
    }
    environment {
        SONARQUBE_URL = 'http://192.168.231.132:9000'
        SONARQUBE_SCANNER = 'SonarQube Scanner'
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
                        def sourceFilePath = '/var/jenkins_home/workspace/code.php'
                        sh "ls -l ${sourceFilePath}" // Just to verify the file is accessible
                        sh "sonar-scanner -Dsonar.projectKey=my-php-project -Dsonar.sources=. -Dsonar.host.url=${env.SONARQUBE_URL} -Dsonar.login=sqp_9dae9af6266f850890e0eab631d0ea053eff2d59"
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

