pipeline{
    agent { label 'demo' }
    options {
        timestamps()
            }
    /*parameters {
        string (name: 'person' defaultValue: 'Naresh' description: 'string parameter')
    }*/
    stages{
        stage ("Git Chekout"){
            steps{
                git branch: 'main', url: 'https://github.com/Narasimha2689/demo-project.git'
            }
        }
        stage ("Unit Testing"){
            steps{
                sh "mvn test"
            }
        }
        stage ("Intigration Testing"){
            steps{
                sh "mvn verify -DskipUnitTests"
            }
        }
        stage ("Maven Build"){
            steps{
                sh "mvn clean install"
            }
        }
        stage ("Static Code Analysis"){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-api-key') {
                    sh 'mvn clean package sonar:sonar'
                }
                }  
            }
        }
        stage ("Quality Gate status"){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-api-key'
                }
            }
        }
        stage ("Upload WAR File To Nexus"){
            steps{
                script{
                    def readPomVersion = readMavenPom file: 'pom.xml'
                    nexusArtifactUploader artifacts: 
                    [
                        [
                            artifactId: 'springboot', 
                            classifier: '', 
                            file: 'target/Uber.jar', 
                            type: 'jar'
                        ]
                    ], 
                            credentialsId: 'Nexus_auth', 
                            groupId: 'com.example', 
                            nexusUrl: '3.110.161.35:8081', 
                            nexusVersion: 'nexus3', 
                            protocol: 'http', 
                            repository: 'demoapp-release', 
                            version: '${readPomVersion.version}'
                }
            }
        }
    }
}