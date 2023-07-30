pipeline {

    agent any

    stages {

        stage('Git checkout'){

            steps{
                git branch: 'main', url: 'https://github.com/SANDEEP-NAYAK/demo-counter-app.git'
            }
        }

        stage('Unit Testing'){

            steps{
                sh 'mvn test'
            }
        }
    }
}