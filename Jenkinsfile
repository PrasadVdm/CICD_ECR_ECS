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
                sh 'mvn install sonar:sonar'
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


      stage("Uploading artifact to Nexus") {
      	steps {
      		nexusArtifactUploader(
		        nexusVersion: 'nexus3',
		        protocol: 'http',
		        nexusUrl: '172.31.28.154:8081',
		        groupId: 'projgrp',
		        version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
		        repository: 'CICDtrivy-release',
		        credentialsId: 'nexuslogin',
		        artifacts: [
		            [artifactId: 'CICDtrivy',
		             classifier: '',
		             file: '**/*.war',
		             type: 'war']
		        ]
     		)
      	}
      }









    }
}

