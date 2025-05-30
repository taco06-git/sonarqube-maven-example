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
