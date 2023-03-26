pipeline {
	tools {
	  maven "MAVEN3"
	  jdk "OracleJDK8"

	}
    agent any

    stages {

      stage ("Fetch Code") {
        steps {
          git branch: "master", url: "https://github.com/PrasadVdm/CICD_Trivy.git"
        }
      }

      stage ("Unit test") {
        steps {
        	sh "mvn test"
        }
      }

      stage("build & SonarQube analysis") {
        steps {
          withSonarQubeEnv('sonar') {
                sh 'mvn sonar:sonar'
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
