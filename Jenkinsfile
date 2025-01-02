pipeline {
    agent any
    
    environment {
        SONAR_TOKEN = credentials('sonar-token') // Add your SonarQube token credentials
        JAVA_HOME = 'C:\\Program Files\\Java\\jdk-17' // Adjust the Java version if needed
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
        
        stage('Run Automation Tests with JaCoCo') {
            steps {
                bat """
                    mvn test jacoco:report
                """
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') { // Adjust 'sonarqube' to match your SonarQube server name in Jenkins
                    bat """
                        mvn sonar:sonar ^
                        -Dsonar.projectKey=mern_2 ^
                        -Dsonar.sources=src/main/java ^
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
