pipeline{
    agent any
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
    }
}