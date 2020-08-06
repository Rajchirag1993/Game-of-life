pipeline {
    agent any
	
environment {

      sonar_url = 'http://172.31.39.179:9000'
      sonar_username = 'admin'
      sonar_password = 'admin'
      nexus_url = '172.31.39.179:8081'
      artifact_version = '0.0.1'

 }
    
    tools {
        jdk 'JAVA_HOME'
        maven 'MAVEN_HOME'
    }

    stages {
        stage('git repo') {
            steps {
             git 'https://github.com/Rajchirag1993/Game-of-life.git'
            }
        }
        stage ('maven') {
            steps {
                sh 'mvn clean install -U  -Dmaven.test.skip=true'
              }
            }
		stage ('Sonarqube Analysis'){
           steps {
           withSonarQubeEnv('sonar') {
           sh '''
           mvn clean package org.jacoco:jacoco-maven-plugin:prepare-agent install -Dmaven.test.failure.ignore=false
           mvn -e -B sonar:sonar -Dsonar.java.source=1.8 -Dsonar.host.url="${sonar_url}" -Dsonar.login="${sonar_username}" -Dsonar.password="${sonar_password}" -Dsonar.sourceEncoding=UTF-8
           '''
           }
         }
      }
	    stage ('Publish Artifact') {
          steps {
          nexusArtifactUploader artifacts: [[artifactId: 'gameoflife', classifier: '', file: "/var/lib/jenkins/workspace/jenkins-task-with-sonarandnexus/gameoflife-build/target/gameoflife-build-1.0-SNAPSHOT.jar", type: 'jar']], credentialsId: '1a678e59-bc68-4532-a927-fd9b058b771b', groupId: 'com.wakaleo.gameoflife', nexusUrl: "172.31.39.179:8081", nexusVersion: 'nexus3', protocol: 'http', repository: 'releases', version: "${artifact_version}"
            }
          }
        
            }
        post {
          always {
          mail to: 'rajesh.devops1993@gmail.com',
          subject: "Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}",
          body: "Build Status: ${currentBuild.currentResult} \n Job Name: ${env.JOB_NAME} \n Build No: ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL} "

          }
      }
}
