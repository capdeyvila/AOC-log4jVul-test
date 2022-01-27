pipeline {
    agent {
        docker {
            image 'maven:3.8.4-jdk-11'
            args '-v /root/.m2:/root/.m2'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
                //build job: 'Burpsuite Scan'
            }
            /*post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }*/
        }
        stage('SonarQube analysis') {
            steps {
                withSonarQubeEnv('Sonarqube') {
                    sh 'mvn dependency-check:check'
                    sh 'mvn clean package sonar:sonar'
                }
            }
        }
        
        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
