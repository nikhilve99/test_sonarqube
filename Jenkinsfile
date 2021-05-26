pipeline {
    agent any
    tools {
        maven 'mvn'
    }
    stages{
        stage('GIT checkout') {
            steps {
                git credentialsId: 'bd82982f-0d08-4114-b7b0-2c7d781f7328', url: 'http://192.168.2.14/nikhilv/new-mvn-proj.git'
            }
        }
          stage('Build Package') {
            steps {
                sh 'mvn clean install'
            }
        }
           stage('code uplaod for report') {
            steps {
                withSonarQubeEnv('sonarqube') {
                   sh 'mvn sonar:sonar'
                }
            }
        }
         stage('code quality check') {
            steps {
                  timeout(time: 1, unit: 'HOURS') {
                      waitForQualityGate abortPipeline: true
                  }                 
                }
            }
          stage('code approval') {
            steps {
                echo "Taking Approval from QA Manager"
                  timeout(time: 1, unit: 'HOURS') {
                      input message: 'Do you want to deploy code?', submitter: 'adu'
                    }                 
                }
            }
            stage('upload code/artifact') {
            steps {
                 nexusArtifactUploader artifacts: [[artifactId: 'embeddedTomcatSample', classifier: '', file: 'target/embeddedTomcatSample.jar', type: 'jar']], credentialsId: 'nexus3', groupId: 'com.heroku.sample', nexusUrl: '192.168.2.63:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'
                }
            }
        }
post {
    failure {
            mail to: 'nikhilv@smartgeeks.in',
            subject: "FAILED: Job ${env.JOB_NAME} [${env.BUILD_NUMBER}]",
            body: "Check console output at '${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]"
        }
    }
    
  }
