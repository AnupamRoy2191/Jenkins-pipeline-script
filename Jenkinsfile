pipeline { 
    agent any
    parameters {
        string(name: 'SERVER_ID', defaultValue: 'artifactory1')
    }
    
    stages {   
        stage('Clone git repo') {   
            steps {   
                git 'https://github.com/royanu91/demo-webapp.git'  
            }   
        }  
        stage('Build Code') {  
            steps {  
                withMaven( maven: 'maven') { 
                sh "mvn clean package" 
                } 
            } 
        }
        
        stage('unit testing') {  
            steps {  
                 
                junit '**/target/surefire-reports/TEST-*.xml'
                archiveArtifacts 'target/*.war'  
                 
            } 
        }

        stage('SonarQube analysis report') { 
//    def scannerHome = tool 'SonarScanner 4.0'; 
        steps{ 
        withSonarQubeEnv('sonarqube') {  
        // If you have configured more than one global server connection, you can specify its name 
//      sh "${scannerHome}/bin/sonar-scanner" 
        withMaven( maven: 'maven') { 
                sh "mvn sonar:sonar" 
                } 
    } 
        } 
        } 
        
            stage ('Upload artifacts to Jfrog Artifactory') {
            steps {
                rtUpload (
                    // Obtain an Artifactory server instance, defined in Jenkins --> Manage Jenkins --> Configure System:
                  
                   serverId: SERVER_ID,
                    spec: """{
                            "files": [
                                    {
                                        "pattern": "/var/lib/jenkins/workspace/tomcat-webapps-pipeline/target/*.war",
                                        "target": "libs-snapshot-local"
                                    }
                                ]
                            }"""
                  
                )
        
            }
        }
        
       stage('Deploy war file on tomcat container') {   
            steps {   
                deploy adapters: [tomcat8(credentialsId: 'tomcat-users', path: '', url: 'http://192.168.0.110:8090')], contextPath: null, war: 'target/*.war'  
            }   
        }
            }  
        }  
