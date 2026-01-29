pipeline {
		agent any
		tools {
			maven "mymaven"
		}
		stages{
		
			stage("Clean Workspace") {//To clean the Workspace which is occupied by the previous build
				steps {
					cleanWs()
				}
			}
			
			stage("Code") {// To get the code from the github
				steps {
					git "https://github.com/shaikmustafa77/dockerwebapp.git"
				}
			}
			
			stage("Build") {// To build the code file and to get the war file
				steps {
					sh "mvn clean package" // we get war file here
					sh "cp -r target Docker-app" // we need to copy the war file to the Docker-app folder where Dockerfile presents to build an image
				}
			}
			
			stage("CQA") {// To test the bugs in the code using sonarqube
				steps {
					withSonarQubeEnv('mysonar') {
						sh "mvn clean verify sonar:sonar -Dsonar.projectKey=MyProject"
					}
				}
			}
			
			stage("Quality Gates") {// To test whether the code is good to build
				steps {
					script {// Select pipeline syntax > select waitforQualitygates
						waitForQualityGate abortPipeline: true, credentialsId: 'sonar-token'
					}
				}
			}
			
			stage("Artifact") {
				steps{ // Go to pipeline syntax and enter all the required credetials
					nexusArtifactUploader artifacts: [[artifactId: 'vprofile', classifier: '', file: 
					'target/vprofile-v2.war', type: 'war']], credentialsId: 'nexus', groupId: 'com.visualpathit', 
					nexusUrl: '3.86.149.224:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'myrepo', 
					version: 'v2'
				}
			}
			
			stage("Image") {
				steps { // Building the image using Dockerfile
					sh "docker build -t appimage Docker-app/"
					sh "docker build -t dbimage Docker-db"
				}
			}
			
			stage("Image Scan") {
				steps { // Using trivy to scan image and get the reports
					sh "trivy image appimage >> appimage-report.txt"
					sh "trivy image dbimage >> dbimage-report.txt"
				}
			}
			stage ("deploy") {
				steps { // deploying the application on the tomcat server
					sh 'docker-compose up -d'
				}
			}
		}
		post {
    always {
        emailext(
            to: "senderemail@gmail.com",
            subject: "Pipeline Status",
            body: """${currentBuild.currentResult}
			Job: ${env.JOB_NAME}
			Build: ${env.BUILD_NUMBER}
			More info: ${env.BUILD_URL}""")
		}	
	}
