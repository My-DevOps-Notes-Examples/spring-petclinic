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
        stage ('Artifactory configuration') {
            steps {
                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "Jfrog_Cloud",
                    releaseRepo: 'spring-pet-clinic-libs-release', // Repository Name
                    snapshotRepo: 'spring-pet-clinic-libs-snapshot' // Repository Name
                )

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "Jfrog_Cloud",
                    releaseRepo: 'spring-pet-clinic-libs-release', // Repository Name
                    snapshotRepo: 'spring-pet-clinic-libs-snapshot' // Repository Name
                )
            }
        }
        stage ('Package') {
            steps {
                rtMavenRun (
                    tool: 'Maven_Default', // Tool name from Jenkins configuration
                    pom: 'pom.xml',
                    goals: 'clean install',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
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
                rtPublishBuildInfo (
                    serverId: "Jfrog_Cloud"
                )
            }
        }
    }
}