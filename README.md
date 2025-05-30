SonarQube and Jenkins (container ) with maven
==============================================

docker run -d --name sonarqube -p 9000:9000 sonarqube:lts

docker run -d --name jenkins -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts


step 1 :  create a custom network
$ docker network create devops-net


Step 2: Connect both containers to devops-net
Attach Jenkins:
$ docker network connect devops-net Jenkins

Attach SonarQube:
$ docker network connect devops-net sonarqube


git repo :  https://github.com/taco06-git/sonarqube-maven-example.git

pipeline : 

pipeline {
    agent any
    tools {
        maven 'MAVEN'  // Uses Maven tool configured in Jenkins
    }
    environment {
        GIT_REPO = 'https://github.com/taco06-git/sonarqube-maven-example.git'
    }
    stages {
        stage('Clone Repository') {
            steps {
                git url: "${GIT_REPO}", branch: 'master'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Code Analysis') {
            steps {
                withSonarQubeEnv('SONARSERVER') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
      
    }
}

Plugins: sonarqube scanner

manage jenkins- credentials - secret text ( add token generated from sonarqube)

manage jenkins - http://sonarqube:9000 (sonarqube is the container , its willl be resolved because container is added to custom network) and then add credentials (secet text + token)

manage jenkins - tools - install sonarque scanner + maven automatically 

