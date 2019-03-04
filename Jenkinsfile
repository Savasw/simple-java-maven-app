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
              withSonarQubeEnv('My SonarQube Server') {
                sh 'mvn sonar:sonar'
              }
            }
          }
		stage("Quality Gate"){
		  timeout(time: 1, unit: 'HOURS') {
			  def qg = waitForQualityGate()
			  if (qg.status != 'OK') {
				  error "Pipeline aborted due to quality gate failure: ${qg.status}"
			  }
		  }
		}
    }
}
