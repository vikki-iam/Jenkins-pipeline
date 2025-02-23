Setting Up Jenkins for Tomcat Deployment..............................

Step 1: Setting Up Jenkins Master-Slave Architecture

Ensure you have a Jenkins master node configured and a Jenkins slave node available for deployment.
The slave node should be properly connected to the master to allow job execution.

Step 2: Installing Tomcat on the Slave Node
Update the system and install Tomcat:

sudo apt update
sudo apt install tomcat10 tomcat10-admin -y


Start Tomcat service:
sudo systemctl start tomcat
sudo systemctl status tomcat


Verify Java Installation (make sure Java is installed):
java -version

Set appropriate permissions for the Tomcat webapps directory:
sudo chown -R tomcat:tomcat /var/lib/tomcat10/webapps
sudo chmod -R 755 /var/lib/tomcat10/webapps


Step 3: Configuring Tomcat User Access
Edit the tomcat-users.xml file to set up user roles and permissions:
sudo vi /etc/tomcat10/tomcat-users.xml
Add the following configuration inside the <tomcat-users> block to enable Jenkins access:

xml
Copy code
<role rolename="manager-gui"/>
<role rolename="admin-gui"/>
<role rolename="manager-script"/>

<!-- Define a user with access to these roles -->
<user username="tomcat" password="password" roles="manager-gui,admin-gui,manager-script"/>
</tomcat-users>
Restart the Tomcat service to apply the changes:


sudo systemctl restart tomcat10
sudo systemctl status tomcat10


Step 4: Configuring Jenkins for Maven and Plugins
Open the Jenkins console and navigate to:

Copy code
Manage Jenkins → Manage Tools → Maven
Add Maven:

Choose the Maven version (e.g., Maven 3.8.5) and click OK.
Download the plugin:

Go to Manage Jenkins → Manage Plugins.
Search for and install the Pipeline Stage View plugin for enhanced pipeline visualization.



Step 5: Forking the Code from Git
Go to your GitHub repository.
Fork the repository to create your own copy.
Copy the URL of the forked repository for use in Jenkins.
Step 6: Writing the Jenkins Pipeline Script
In Jenkins, create a new pipeline job.

In the pipeline script section, write your pipeline code. Example:

groovy
Copy code
pipeline {
    agent any
    environment {
        TOMCAT_SERVER_IP = "your-slave-node-ip"
        TOMCAT_WEBAPPS_DIR = "/var/lib/tomcat10/webapps"
    }
    tools {
        maven 'Maven 3.8.5'
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/your-repo-name.git'
            }
        }
        stage('Build') {
            steps {
                script {
                    sh 'mvn clean install -Dmaven.test.skip=true'
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    sshagent(['tomcat-ssh']) {
                        sh '''
                            ssh ubuntu@${TOMCAT_SERVER_IP} "
                                sudo systemctl stop tomcat10 &&
                                sudo rm -rf ${TOMCAT_WEBAPPS_DIR}/ROOT ${TOMCAT_WEBAPPS_DIR}/ROOT.war &&
                                sudo mv /home/ubuntu/workspace/my-pipeline/target/ROOT.war ${TOMCAT_WEBAPPS_DIR}/ROOT.war &&
                                sudo systemctl start tomcat10"
                        '''
                    }
                }
            }
        }
    }
    post {
        always {
            echo 'Cleaning up workspace...'
            deleteDir()
        }
        success {
            echo 'Deployment completed successfully.'
        }
        failure {
            echo 'Deployment failed. Please check the logs for errors.'
        }
    }
}
Ensure that your students follow these steps in sequence to set up and configure Jenkins for deploying a Maven-built web application to a Tomcat server.
