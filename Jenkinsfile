pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('sonarqube-token')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/DevaseeshKumar/StudentActivityPortal_TermPaper.git'
            }
        }

        stage('Build Maven Package') {
            steps {
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    bat """
                        mvn clean verify sonar:sonar ^
                          -Dsonar.projectKey=StudentActivityPortal ^
                          -Dsonar.host.url=http://localhost:9000 ^
                          -Dsonar.login=%SONAR_TOKEN%
                    """
                }
            }
        }

        stage('Dependency Vulnerability Scan') {
            steps {
                bat 'mvn org.owasp:dependency-check-maven:check -Dformat=ALL'
                archiveArtifacts artifacts: 'target/dependency-check-report.*', fingerprint: true
            }
        }

        stage('Test') {
            steps {
                echo "✅ Test Completed"
            }
        }

        stage('Start Services with Docker Compose') {
            steps {
                bat 'docker-compose up -d --build'
            }
        }
    }

    post {
        success {
            echo '🎉 Pipeline executed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Please check logs.'
        }
        cleanup {
            cleanWs()
        }
    }
}
