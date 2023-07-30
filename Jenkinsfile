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

        stage('Integration testing'){

            steps{
                sh 'mvn verify -DiskpUnitTests'
            }
        }

        stage('Maven Build'){

            steps{
                sh 'mvn clean install'
            }
        }

       stage('Static code analysis'){

            steps{
                script{
                        withSonarQubeEnv(credentialsId: 'sonar-api') {
                        sh 'mvn clean package sonar:sonar'
                    }
                }
                
            }
        }

        stage('Quality Gate Status'){

            steps{

                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-api'
                }
            }
        }

        stage('Upload Artifact to nexus'){

            steps{

                script{

                    def readPomVersion = readMavenPom file: 'pom.xml'

                    def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "JavaApp-SNAPSHOT" : "JavaAppRelease"
                    nexusArtifactUploader artifacts: [
                        [
                            artifactId: 'springboot',
                            classifier: '',
                            file: 'target/Uber.jar',
                            type: 'jar'
                        ]

                    ],

                    credentialsId: '8d668bb7-e0fa-4cc0-9b30-c2f440263050',
                    groupId: 'com.example',
                    nexusUrl: '172.190.201.24:8081',
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    repository: nexusRepo,
                    version: "${readPomVersion.version}"

                }
            }
        }

        stage('Docker Image Build'){

            steps{
                script{
                    sh 'whoami'
                    sh 'docker image build -t java-application:v1.$BUILD_ID .'
                    sh 'docker image tag java-application:v1.$BUILD_ID sandeepdarkworld/java-application:v1.$BUILD_ID'
                    
                }
            }
        }

        stage('Pushing Docker Image to DockerHub'){

            steps{

                script{
                    withCredentials([usernamePassword(credentialsId: 'dockerhubcred', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh 'docker login -u \$DOCKER_USERNAME -p \$DOCKER_PASSWORD'
                        sh'docker push sandeepdarkworld/java-application:v1.$BUILD_ID'
                    }
                }
            }
        }
    }
}