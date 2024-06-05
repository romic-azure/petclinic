@Library("myShared-library") _

pipeline {
    agent any
    tools{
        jdk 'JDK8'
        maven 'maven3.9.6'
    }
    triggers {
        pollSCM "H/15 9-17 * * 1-5"
       }

    stages{
        
        stage("Shows who started the job/pipeline") {
            steps {     			
                whoStarted()
            }
        }
        
        stage("Compile"){
            steps{
                echo '-===- Compile petclinic app -===-'
                sh "mvn -B -DskipTests clean compile"
            }
        }

         stage("Test Cases"){
            steps{
                echo '-===- Testing petclinic app -===-'
                sh "mvn test"
            }
             post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

         stage("Build"){
            steps{
                echo '-===- Building the petclinic app -===-'
                sh "mvn clean install"
            }
        }
        stage("Build docker image"){
            steps{
                echo '-===- Create docker image -===-'
                sh "docker build -t romeops/devops:$env.BUILD_NUMBER ."
            }
        }
        stage("Publish docker image"){
            steps{
                echo '-===- Pushing petclinic docker image -===-'
                script{
                    withCredentials([usernamePassword(credentialsId: "docker-credentials", usernameVariable: "DOCKER_REPOSITORY_USER", passwordVariable: "DOCKER_REPOSITORY_PASSWORD")]){
                        // sh "docker login -u $DOCKER_REPOSITORY_USER -p $DOCKER_REPOSITORY_PASSWORD"
                        sh "echo $DOCKER_REPOSITORY_PASSWORD | docker login -u romeops --password-stdin"
                        sh "docker push romeops/devops:$env.BUILD_NUMBER"
                    }
                }
            }
        }

        stage('Remove local images') {
            steps {
                echo '-===- Delete the local docker images -===-'
                sh("docker rmi -f romeops/devops:$env.BUILD_NUMBER")
            }
        }
        
    }
}
