pipeline {
	tools {
	  mvn "MAVEN3"

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
    }
}
