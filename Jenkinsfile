@Library("myShared-library") _

pipeline {
    agent any
    tools{
        jdk 'JDK8'
        maven 'maven3.9.6'
    }

    stages{
        
        stage("Shows who started the job/pipeline") {
            steps {     			
                whoStarted()
            }
        }
        
        stage("Compile"){
            steps{
                sh "mvn clean compile"
            }
        }

         stage("Test Cases"){
            steps{
                sh "mvn test"
            }
        }

         stage("Build"){
            steps{
                sh "mvn clean install"
            }
        }
        stage("Build docker image"){
            steps{
                sh "docker build -t romeops/devops:$env.BUILD_NUMBER ."
            }
        }
        stage("Publish docker image"){
            steps{
                script{
                    withCredentials([usernamePassword(credentialsId: "docker-credentials", usernameVariable: "DOCKER_REPOSITORY_USER", passwordVariable: "DOCKER_REPOSITORY_PASSWORD")]){
                        sh "docker login -u $DOCKER_REPOSITORY_USER -p $DOCKER_REPOSITORY_PASSWORD"
                        sh "docker push romeops/devops:$env.BUILD_NUMBER"
                    }
                }
            }
        }
    }
}
