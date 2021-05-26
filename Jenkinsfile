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
          
    }
    
  }
