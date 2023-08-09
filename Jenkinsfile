pipeline {
    agent any
    tools {
        maven 'maven3.9.4'
    }
    stages {
        stage('Git Checkout'){
            steps {
                git branch: 'main', credentialsId: 'github-leh-india', url: 'https://github.com/leh-india/test-repo1.git'
            }
        }
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
        }
        stage('SonarQube analysis') {
//          def scannerHome = tool 'SonarScanner 4.0';
            steps{
                withSonarQubeEnv('sonar-jenkins') { 
//              If you have configured more than one global server connection, you can specify its name
//              sh "${scannerHome}/bin/sonar-scanner"
                sh "mvn sonar:sonar"
                }
            }
        }
        stage('Push to Artifactory'){
            steps {
                rtUpload (
                serverId: 'artifactory',
                spec: '''{
                      "files": [
                        {
                          "pattern": "*.war",
                           "target": "onlinebook-snapshot/com/obs/onlinebooksotre/"
                        }
                    ]
                }'''
              )
            }
        }
        stage('Deploy to Tomcat') {
            steps {
                sshagent(['tomcat-server']) {
                    sh "scp -o StrictHostKeyChecking=no target/*.war ubuntu@13.235.9.3:/opt/apache-tomcat-10.1.11/webapps/" 
                }
            }
        }
    }
}