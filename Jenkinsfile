pipeline {
    agent {
        label 'job-1' // This specifies that the pipeline should run on the node labeled 'job-1'
    }
    // environment {
    //     TOMCAT_SERVER_IP = "13.233.160.204" // Update with your Tomcat server's public IP
    //     TOMCAT_WEBAPPS_DIR = "/var/lib/tomcat10/webapps" // Tomcat's webapps directory for deployment
    // }
    tools {
        maven 'Maven 3.8.1' // Ensure Maven is configured in Jenkins
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/vikki-iam/Jenkins-pipeline.git'
            }
        }
        stage('Build') {
            steps {
                script {
                    // Build the project and skip tests
                    sh 'mvn clean install -Dmaven.test.skip=true'
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    // Remove the old ROOT.war file if it exists
                    sh 'sudo rm -f /var/lib/tomcat10/webapps/ROOT.war'
                    // Copy the new WAR file to Tomcat's webapps directory
                    sh 'cp /home/ubuntu/workspace/my-pipeline/target/ROOT.war /var/lib/tomcat10/webapps'
                }
            }
        }
    }
}
