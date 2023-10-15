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
            post {
                success{
                    withSonarQubeEnv(credentialsId: 'sonarqube') {
                        sh 'mvn sonar:sonar'
                    }
                }
                unsuccessful {
                    slackSend baseUrl: 'https://hooks.slack.com/services/', 
                    channel: 'jenkins-notifs', 
                    color: '#FF0000', 
                    message: 'BUILD FAILED: Check Unit Tests! ', 
                    tokenCredentialId: 'slack', 
                    username: 'Jenkins-NOTIFS'
                }
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
        stage ('SLACK NOTIFICATION'){
            steps {
                slackSend baseUrl: 'https://hooks.slack.com/services/', 
                channel: 'jenkins-notifs', 
                color: '	#008000', 
                message: 'BUILD SUCCESS!', 
                tokenCredentialId: 'slack', 
                username: 'Jenkins-NOTIFS'
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
