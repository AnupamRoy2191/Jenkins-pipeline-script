pipeline { 
    agent any
    parameters {
        string(name: 'SERVER_ID', defaultValue: 'artifactory1')
    }
    
    triggers { 
        pollSCM('* * * * *') 
    }
    stages {   
        stage('Clone git repo') {   
            steps {   
                git 'https://github.com/royanu91/Tomcat-Webapp.git'  
            }   
        }  
        stage('Build Code') {  
            steps {  
                withMaven( maven: 'Maven') { 
                sh "mvn clean package" 
                } 
            } 
        } 

        stage('SonarQube analysis') { 
//    def scannerHome = tool 'SonarScanner 4.0'; 
        steps{ 
        withSonarQubeEnv('sonarqube') {  
        // If you have configured more than one global server connection, you can specify its name 
//      sh "${scannerHome}/bin/sonar-scanner" 
        withMaven( maven: 'Maven') { 
                sh "mvn sonar:sonar" 
                } 
    } 
        } 
        } 
        
            stage ('Upload file') {
            steps {
                rtUpload (
                    // Obtain an Artifactory server instance, defined in Jenkins --> Manage Jenkins --> Configure System:
                  
                   serverId: SERVER_ID,
                    spec: """{
                            "files": [
                                    {
                                        "pattern": "/var/lib/jenkins/workspace/Tomcat-webapps-pipeline/target/*.war",
                                        "target": "libs-snapshot-local"
                                    }
                                ]
                            }"""
                  
                )
        
            }
        }
        
       stage('Deploy package on tomcat container') {   
            steps {   
                deploy adapters: [tomcat8(credentialsId: 'tomcat-users', path: '', url: 'http://192.168.0.111:8080')], contextPath: null, war: 'target/*.war'  
            }   
        }
            }  
        }  
