SonarQube and Jenkins (container ) with maven
==============================================

docker run -d --name sonarqube -p 9000:9000 sonarqube:lts

docker run -d --name jenkins -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts


step 1 :  create a custom network
------------------------------------
$ docker network create devops-net


Step 2: Connect both containers to devops-net
Attach Jenkins:
--------------------------------------------------

$ docker network connect devops-net Jenkins

Attach SonarQube:
---------------------------------------------------
$ docker network connect devops-net sonarqube

REPO
-------------------------------------------------------------------------
git repo :  https://github.com/taco06-git/sonarqube-maven-example.git

pipeline : 
----------------------------------------------------------------------
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
---------------------------------------------------------------------------

manage jenkins- credentials - secret text ( add token generated from sonarqube)

manage jenkins - http://sonarqube:9000 (sonarqube is the container , its willl be resolved because container is added to custom network) and then add credentials (secet text + token)

manage jenkins - tools - install sonarque scanner + maven automatically 

****JAVA
Since you're running Jenkins inside a Docker container, you need to install Java within the container itself. By default, the official Jenkins Docker image comes with a pre-installed version of Java. However, if you need a specific version or encounter issues, here’s what you can do:

$ docker exec -it jenkins java --version 
openjdk 21.0.7 2025-04-15 LTS OpenJDK Runtime Environment Temurin-21.0.7+6 (build 21.0.7+6-LTS) OpenJDK 64-Bit Server VM Temurin-21.0.7+6 (build 21.0.7+6-LTS, mixed mode)

now in jenkins console , i go to manage jenkins , tools , what path should i set for JAVA_HOME ??
$ docker exec -it jenkins bash -c 'readlink -f $(which java)'

This should return something like:  /opt/java/openjdk/bin/java   The JAVA_HOME path is everything up to /bin/java, so in this case, it would be:
JAVA_HOME=/opt/java/openjdk

------------

tools cn be provided in two ways 

1) if u have installed maven on local ec2 instance manually .
pipeline{
    agent any
    environment {
        PATH = "$PATH:/opt/apache-maven-3.8.2/bin"
    }

2.  if you are using maven from Jenkins - manage jenkins- tools - install automatically 

pipeline {
    agent any
    tools {
        maven 'MAVEN'  // Uses Maven tool configured in Jenkins
    }



archetype:
mvn archetype:generate -DgroupId=com.example -DartifactId=myapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false



