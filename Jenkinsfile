pipeline{
    agent { label 'demo' }
    options {
        timestamps()
            }
    /*parameters {
        string (name: 'person' defaultValue: 'Narfdsesh' description: 'string parameter')
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
                    def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "demoapp-snapshot" : "demoapp-release"
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
                            nexusUrl: '43.205.203.235:8081', 
                            nexusVersion: 'nexus3', 
                            protocol: 'http', 
                            repository: "${nexusRepo}", 
                            version: "${readPomVersion.version}"
                }
            }
        }
        stage('Docker Image Build'){
            steps{
                script{
                    sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                    sh 'docker image tag $JOB_NAME:v1.$BUILD_ID narasimha2689/$JOB_NAME:v1.$BUILD_ID'
                    sh 'docker image tag $JOB_NAME:v1.$BUILD_ID narasimha2689/$JOB_NAME:latest'
                }
            }
        }
        stage('Push Image to DockerHub'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'DockerCred', variable: 'DockerCred')]) {
                        sh 'docker login -u narasimha2689 -p ${DockerCred}'
                        sh 'docker image push narasimha2689/$JOB_NAME:v1.$BUILD_ID'
                        sh 'docker image push narasimha2689/$JOB_NAME:latest'
                    }
                }
            }
        }
    }
}