#!/usr/bin/env grrovy
/* Declarative pipeline must be enclosed within a pipeline block */
pipeline {
     /*agent section specifies where the entire Pipeline script will execute in the Jenkins environment */
    //agent any
	agent {
	
	label 'master'
	
	}
    
	/* Here we can declare the environment variables */
    environment {
      CF_VERSION = '1.0'
      CF_API = ''
      DEPLOY_ENV = "${params.environment}"
   }
    /**
     * tools to auto-install and put on the PATH
     * some of the supported tools - maven, jdk, gradle,git
     */
    tools {
        maven 'maven3.6.3' 
    }
    
    triggers {
   
     pollSCM('* * * * *')
    
   }
   
   
   // Configure Pipeline-specific options
   options {
      
       // Add timestamps to output
       timestamps()
       overrideIndexTriggers(false)
       // timeout job after 60 minutes
       timeout(time: 60,unit: 'MINUTES')
       // Keep only last 5 builds
       buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5'))
       disableConcurrentBuilds()
   }
 
     /**
     * stages contain one or more stage directives
     */
    stages {
       
        
        stage('CheckoutCode') {
            steps {
                git branch: 'development', credentialsId: '26beb6b9-b6b7-452e-ada7-355d0bb08cc3', url: 'https://github.com/ramorganizations/maven-web-application.git'
                sh "echo CF_VERSION ${CF_VERSION}"
            }
        }
        
        stage('MavenBuild') {
            steps {
               sh 'mvn clean package'
            }
        }
        
        stage('SonarQubeReportExecution') {
            steps {
               sh 'mvn sonar:sonar'
            }
        }
        
        stage('UploadArtifactIntoNexus') {
            steps {
               sh 'mvn deploy'
            }
        }
        
        
        stage('DeployApplicationIntoTomcat') {
            steps {
            // provide SSH credentials to builds via a ssh-agent in Jenkins
              sshagent(['9a1cb013-a4be-4870-ae7e-3013c0bd3a12']) {
                sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@18.118.50.55:/opt/apache-tomcat-9.0.46/webapps/"
               } 
            }
        }
        
    }
    /**
     * post section defines actions which will be run at the end of the Pipeline run or stage
     * post section condition blocks: always, changed, failure, success, unstable, and aborted
     */
     
    post {
        always {
            cleanWs()
        }
        success {   
               mail to: 'bhulakshmidondeti@gmail.com',
                    bcc: 'bhulakshmidondeti@gmail.com',
                    cc: 'bhulakshmidondeti@gmail.com', 
                    from: 'bhulakshmidondeti@gmail.com', 
                    replyTo: 'bhulakshmidondeti@gmail.com',
                    subject: "Pipeline Build is over .. Build # is ..${env.BUILD_NUMBER} and Build status is.. ${currentBuild.result}.",
                    body: "Pipeline Build is over .. Build # is ..${env.BUILD_NUMBER} and Build status is.. ${currentBuild.result}."
                   
            cleanWs()
        }
        failure {
            mail to: 'bhulakshmidondeti@gmail.com',
                    bcc: 'bhulakshmidondeti@gmail.com',
                    cc: 'bhulakshmidondeti@gmail.com', 
                    from: 'bhulakshmidondeti@gmail.com', 
                    replyTo: 'bhulakshmidondeti@gmail.com',
                    subject: "Pipeline Build is over .. Build # is ..${env.BUILD_NUMBER} and Build status is.. ${currentBuild.result}.",
                    body: "Pipeline Build is over .. Build # is ..${env.BUILD_NUMBER} and Build status is.. ${currentBuild.result}."
                   
        }
    }
}
