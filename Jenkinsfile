pipeline {
    agent any
    triggers {
        githubPush()
    }
    environment {
        SONARQUBE_URL = 'http://sonarqube:9000'
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
        stage('Copy Code') {
            steps {
                script {
                    // Copier le fichier code.php dans le workspace Jenkins
                    // Assurez-vous que le chemin est correct et accessible depuis Jenkins
                    def sourceFilePath = '/home/rayleair/Desktop/code/code.php'
                    def destinationDir = "${env.WORKSPACE}"
                    sh "cp ${sourceFilePath} ${destinationDir}/"
                }
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv('SonarQube') {
                        sh 'sonar-scanner -Dsonar.projectKey=my-php-project -Dsonar.sources=. -Dsonar.host.url=$SONARQUBE_URL -Dsonar.login=<sqp_9dae9af6266f850890e0eab631d0ea053eff2d59>'
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
