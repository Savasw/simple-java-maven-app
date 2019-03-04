pipeline {
	agent {
        docker {
            image 'maven:3-alpine' 
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
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
		stage("SonarQube analysis") {
            steps {
              withSonarQubeEnv('SonarQube') {
			  def scannerHome = tool 'SonarQube Scanner 2.8';
                sh "${scannerHome}/bin/sonar-scanner"
              }
            }
        }
		stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
        }
    }
}
