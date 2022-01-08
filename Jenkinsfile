pipeline {
    environment {
    registry = "dev/timesheet"
    registryCredential = 'dockerHub'
    dockerImage = ''
    }
    agent any

    stages {
       stage ('Cloning Project From Git') {
            steps {
                 git branch: "DevTimeSheet",
                     url: "https://github.com/hooLeeFuk/DevTimeSeet.git",

            }
       }
       stage("Build") {
            steps {
                bat "mvn compile"
            }
       }
       stage("Unit Test"){
            steps{
                bat "mvn test"
            }
       }
       stage("Static test") {
            steps {
                bat "mvn sonar:sonar -Dsonar.projectKey=timesheet -Dsonar.host.url=http://localhost:9000 -Dsonar.login=66eb66c8b107ea31c937803fc44077efba9fb228"
            }
       }
       stage("Clean And Packaging")
       {
            steps {
                bat "mvn clean package"
            }
       }
       stage("Deploy With Nexus") {
            steps {
                bat "mvn clean package deploy:deploy-file -DgroupId=tn.spring -DartifactId=timesheet -Dversion=1.0 -DgeneratePom=true -Dpackaging=jar -DrepositoryId=deploymentRepo -Durl=http://localhost:8081/repository/maven-releases/ -Dfile=target/timesheet-1.0.war"
            }
       }
       stage('Building our image') {
            steps{
                 script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                 }
            }
       }
       stage('Deploy our image') {
             steps {
                  script {
                     docker.withRegistry( '', registryCredential ) {
                     dockerImage.push()
                      }
                  }
            }
       }
       stage('Cleaning up') {
             steps {
                   bat "docker rmi $registry:$BUILD_NUMBER"
                    }
            }
       stage('Pulling MySQL') {
             steps {
                    bat "docker pull mysql"
                    }
             }
       stage('Pulling Project') {
             steps {
                    bat "docker pull dev/timesheet:14"
                    }
             }
    }
       post{
            always{
                 emailext body: 'New update comming', recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], subject: 'Timesheet'
                 cleanWs()
                 }
            }
}
