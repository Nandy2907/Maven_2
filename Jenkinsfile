pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('sonar-token') // SonarQube token
        JAVA_HOME = 'C:\\Program Files\\Java\\jdk-17' // Path to Java JDK
        PATH = "${JAVA_HOME}\\bin;${env.PATH}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build with Maven') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Run Tests and Generate Coverage Report') {
            steps {
                bat 'mvn test jacoco:report'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') { // Ensure this matches your SonarQube server in Jenkins
                    bat """
                        mvn sonar:sonar ^
                        -Dsonar.projectKey=AutomationProject ^
                        -Dsonar.sources=src/test/java ^
                        -Dsonar.tests=src/test/java ^
                        -Dsonar.java.binaries=target/classes ^
                        -Dsonar.java.test.binaries=target/test-classes ^
                        -Dsonar.host.url=http://localhost:9000 ^
                        -Dsonar.login=%SONAR_TOKEN% ^
                        -Dsonar.java.coveragePlugin=jacoco ^
                        -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}
