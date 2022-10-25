pipeline{
    agent { label 'demo' }
    options {
        timestamps
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
        /*stage ("Static Code Analysis"){
            steps{
                
            }
        }*/
    }
}