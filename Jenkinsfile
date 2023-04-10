pipeline {
	tools {
	  maven "MAVEN3"
	  jdk "OracleJDK8"

	}
  environment {
    registryCredentials = 'ecr:us-east-1:aws-ecr-ecs-user'
    ecrAppImage = '161589005609.dkr.ecr.us-east-1.amazonaws.com/vdm-ecr-ecs'
    ecrRegistryURL = 'https://161589005609.dkr.ecr.us-east-1.amazonaws.com'
  }
    agent any

    stages {

      stage ("Fetch Code") {
        steps {
          git branch: "master", url: "https://github.com/PrasadVdm/CICD_ECR_ECS.git"
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
		        nexusUrl: '172.31.25.2:8081',
		        groupId: 'projgrp',
		        version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
		        repository: 'CICDEcrEcs',
		        credentialsId: 'nexuslogin',
		        artifacts: [
		            [artifactId: 'ECRECS',
		             classifier: '',
		             file: 'target/vprofile-v2.war',
		             type: 'war']
		        ]
     		)
      	}
      }

      stage("Build Image") {
        steps {
          script {
            dockerImage = docker.build( ecrAppImage + ":$BUILD_NUMBER", "${WORKSPACE}/docker-files")
          }

        }
      }

      stage("Upload Image") {
        steps {
          script {
            docker.withRegistry( ecrRegistryURL, registryCredentials ) {
              dockerImage.push("$BUILD_NUMBER")
              dockerImage.push('latest')
            }
          }
        }
      }









    }
}

