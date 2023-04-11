
pipeline {
	agent any
	
	stages{
	stage('Checkout Code'){
		checkout scm
	}
	
	stage('Build'){
		bat "mvn clean install -Dmaven.test.skip=true"
	}
	
	stage('Test Case Execution'){
		bat "mvn clean org.jacoco:jacoco-maven-plugin:prepare-agentinstall-Pcoverage-per-test"
	}
	
	stage('Archive Artifact'){
		archiveArtifacts artifacts:'target/8.war'
	}
	
	stage('deployment'){
		//deploy adapters: [tomcat9(credentialsId: 'TomcatCreds' path: '', url: 'http://34.207.125.36:8080/')], contextPath: 'counterwebapp', war: 'target/*.war'
		deploy adapters: [tomcat9(url: 'http://34.207.125.36:8080/', 
                              credentialsId: 'TomcatCreds')], 
                     war: 'target/*.war',
                     contextPath: 'app'
		
		
	}
	
	stage('Notification'){
		emailext(
			subject: "Job Completed",
			body: "Jenkins pipeline job for maven build job completed",
			to: "sudheer.barakers@gmail.com"
		)
	}
	}
}
