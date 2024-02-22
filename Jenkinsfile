pipeline {
    agent { label 'Jdk17_Maven' }
    triggers { pollSCM('* * * * *') }
    stages {
        stage('VCS') {
            steps {
                git url: 'https://github.com/My-DevOps-Notes-Examples/spring-petclinic.git',
                    branch: 'declarative'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Sonar-Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                sh 'mvn clean package sonar:sonar -Dsonar.organization=jenkinswithsonar -Dsonar.projectKey=jenkinswithsonar'
              }
            }
        }
        stage('Post-Build') {
            steps {
                archiveArtifacts artifacts: '**/target/*.jar',
                         allowEmptyArchive: false,
                         onlyIfSuccessful: true
                junit testResults: '**/surefire-reports/TEST-*.xml',
                      allowEmptyResults: false
            }
        }
    }
}