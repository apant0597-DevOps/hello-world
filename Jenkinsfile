
pipeline {
    agent any
    tools {
	    maven 'maven-jenkins'
    }
    
    stages {
        stage('MAVEN BUILD'){
            steps{
                sh 'mvn clean package -Dmaven.test.skip=true'
                sh 'mv webapp/target/*.war webapp/target/helloworld.war'
            }
        }
        stage('TEST'){
            steps{
                sh 'mvn test'
            }
        }
        stage('DEPLOY'){
            steps {
                sshagent(['jenkins-agent-creds']) {
                    sh '''
                
                    scp -o StrictHostKeyChecking=no webapp/target/helloworld.war ec2-user@54.191.0.47:/opt/tomcat9/webapps
                    ssh ec2-user@54.191.0.47 /opt/tomcat9/bin/shutdown.sh
                    ssh ec2-user@54.191.0.47 /opt/tomcat9/bin/startup.sh
                
                    '''
                }
            }
        }
    }
    
    post {
        success { 
            echo "This pipeline is successfull!"
            }
    unsuccessful {
            echo "ISSUE!!!"
            }
    }
}
