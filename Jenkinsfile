pipeline {
    
    agent any
    
    tools {
        maven 'Maven_3'
    
    }
    
    stages {
        stage ('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: '50f6fddf-d641-43aa-ac88-881339c4ed19', url: 'https://github.com/akannan1087/myJan2021Repo']]])
            }
        }
        
        stage ('Build') {
            steps {
             sh 'mvn clean install -f MyWebApp/pom.xml'
            }
        }
        
        stage ('Code scan') {
            steps {
                    withSonarQubeEnv('My_SonarQube') {
                    sh 'mvn -f MyWebApp/pom.xml sonar:sonar'
                }
            }
        }
        
        stage ('Jacoco') {
            steps {
                jacoco()
            }
        }
        
        stage ('Nexus upload') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'MyWebApp', classifier: '', file: 'MyWebApp/target/MyWebApp.war', type: 'war']], credentialsId: 'updated nexus credentials', groupId: 'com.cap.one', nexusUrl: 'ec2-3-138-105-246.us-east-2.compute.amazonaws.com:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'
            }
        }
        
        stage ('DEV Deploy') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'f62d2453-5120-45d3-b5e0-2cdf5e3fb381', path: '', url: 'http://ec2-18-191-161-113.us-east-2.compute.amazonaws.com:8080')], contextPath: null, war: '**/*.war'
            }
            
        }
        stage ('Slack notify') {
            steps {
            slackSend channel: 'mar-2021-weekday-batch', message: 'DEV Deployment was successful, please start smoke testing'
            }
        }
    

         
      }
}
