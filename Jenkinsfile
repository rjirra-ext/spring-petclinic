pipeline {
    agent any
    tools {
      maven "M3"
    }
    environment {
      pass = "${env.ocpass}"
      usr = "${env.ocuser}"
    }
    parameters {
        string(name: 'PROJECT_NAME', defaultValue: 'drivewealth-posttrade-acats-service', description: 'Give repo name here')
    }
    triggers {
        pollSCM('H/15 * * * *')
    }
    options {
    buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '5', numToKeepStr: '10')
    }
    stages {
        stage('Build') {
            steps {
                sh "mvn clean install -Dmaven.repo.local=/var/jenkins_home/.m2/repository"
            }
        }
      
        stage('SonarQube') {
            environment {
                    SCANNER_HOME = tool 'SonarQubeScanner'
                    ORGANIZATION = "rjirra-ext"
                    SONAR_USER_HOME="/var/jenkins_home/.sonar"
                    PROJECT_NAME="rjirra-ext_spring-petclinic"
              
                }
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.organization=$ORGANIZATION \
                    -Dsonar.java.binaries=target/classes \
                    -Dsonar.projectKey=$PROJECT_NAME \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=https://sonarcloud.io'''
                }
            }
        }
        stage("Quality Gate") {
        steps {
            timeout(time: 1, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
               }
            }
        }
        stage('Workspace') {
            steps {
                cleanWs()
                echo "Performed the cleaning process for the mono repo workspace"
            }
        }
    }
}
