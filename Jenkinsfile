pipeline{

    agent any

    stages{

        stage("git checkout"){

            steps{
                git branch: 'main', url: 'https://github.com/clemenrance/static-website.git'
            }
        }
        stage("Unit Test"){

            steps{
                sh 'mvn test'
            }
        }
        stage("Integration Test"){

            steps{
                sh 'mvn verify -DskipUnitTests'
            }
        }
        stage("Maven Build"){

            steps{
                sh 'mvn clean install'
            }
        }
        stage("Static Test Analysis in Sonarqube"){

            steps{

                script{
                    withSonarQubeEnv(credentialsId: 'sonar-auth'){  
                    sh 'mvn clean package sonar:sonar'
                    }
                }
            }
        }
        stage("Quality gate analysis"){
            
            steps{

                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-auth'
                }
            }
        }
        stage("Deploying to tomcat"){

            steps{
                    sshagent(['wanyu']){  
                    sh 'scp -o StrictHostKeyChecking=no /root/.jenkins/workspace/website/target/webbi-1.0-SNAPSHOT.jar ubuntu@172.31.13.73:/var/lib/tomcat9/webapps'
                }
            }
         }
    }
}