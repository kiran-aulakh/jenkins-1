pipeline{
    agent any
    tools {
        maven 'Maven3'
    }
    stages{
        stage('Checkout'){
            steps{
                git 'https://github.com/kiran-aulakh/jenkins-1.git'
            }
        }
        stage('Build'){
            steps{
                bat 'mvn -f Hello-World-JAVA-master/pom.xml clean package -Dmaven.test.failure.ignore=true '
            }
        }
        stage('Test'){
            steps{
                bat 'mvn -f Hello-World-JAVA-master/pom.xml clean test'
            }
        }
        stage('SonarQube'){
            steps{
                withSonarQubeEnv(installationName: 'Sonar1') {
                    bat 'mvn -f Hello-World-JAVA-master/pom.xml org.sonarsource.scanner.maven:sonar-maven-plugin:3.7.0.1746:sonar'
                }
            }
        }
        stage('Artifactory'){
            steps{
                script{
               def server = Artifactory.server('122@arr');
               def mvv = Artifactory.newMavenBuild();
               mvv.resolver server:server,  releaseRepo:'libs-release',snapshotRepo:'libs-snapshot'
               mvv.deployer server:server, releaseRepo:'libs-release-local',snapshotRepo:'libs-snapshot-local'
               mvv.tool='Maven3'
               def buildInfo= mvv.run pom: 'Hello-World-JAVA-master/pom.xml', goals: '-U clean install'
               server.publishBuildInfo buildInfo}
            }
        }
        stage('Docker image'){
            steps{
                dir('Hello-World-JAVA-master/'){
                     bat 'docker build -t kiranbirkaur/i-kiranbirkaur-master%BUILD_NUMBER% --no-cache -f Dockerfile .'
                }
            }
        }
        stage('Docker push'){
            steps{
                 dir('Hello-World-JAVA-master/'){
                     bat 'docker push kiranbirkaur/i-kiranbirkaur-master%BUILD_NUMBER% '
                 }
            }
        }
        stage('Docker deployment'){
            steps{
                 dir('Hello-World-JAVA-master/'){
                     bat 'docker run --name kiranbirkaur/i-kb-%BUILD_NUMBER% -d -p 6000:7000 kiranbirkaur/i-kiranbirkaur-master%BUILD_NUMBER%'
                 }
            }
        }
    }
}