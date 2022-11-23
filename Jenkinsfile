pipeline {
    agent any
    tools 
    {
        maven 'maven_env'
    }
    stages {
        stage(' Compile ') {
            steps {
                echo 'TODO: build'
                sh "./mvnw clean compile -e"
            }
        }
        stage('Test') {
            steps {
                echo 'TODO: test'
                sh "./mvnw clean test -e"
            }
        }
        stage('Jar') {
            steps {
                echo 'TODO: package'
                sh "./mvnw clean package -e"
            }
        }
        stage('Sonarque')
        {
            steps{
                withSonarQubeEnv(credentialsId: 'sonar_token', installationName: 'sonarqube_env')
                {
                sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.7.0.1746:sonar'
                }
            }
        }
        stage('Maven Artifact') 
        {
            steps 
            {
                echo 'TODO: Maven'
                sh "./mvnw clean install"
            }
        }
        stage('ToRepository')
        {
            steps
            {
                nexusPublisher nexusInstanceId: 'nexus_docker', nexusRepositoryId: 'devops-usach-nexus', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: "${WORKSPACE}/build/DevOpsUsach2020-0.0.1.jar"]], mavenCoordinate: [artifactId: 'DevOpsUsach2020', groupId: 'com.devopsusach2020', packaging: 'jar', version: '0.0.1']]]
            }
        }
        stage ('Send to  Nexus 1.0.0')
        {
            steps 
            {
                echo 'TODO: Maven Install to version 1.0.0'
                sh "./mvnw versions:set -DnewVersion=1.0.0"
                sh "./mvnw clean package -e"
                sh "./mvnw clean install" 
                nexusPublisher nexusInstanceId: 'nexus_docker', nexusRepositoryId: 'devops-usach-nexus', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: "${WORKSPACE}/build/DevOpsUsach2020-1.0.0.jar"]], mavenCoordinate: [artifactId: 'DevOpsUsach2020', groupId: 'com.devopsusach2020', packaging: 'jar', version: '1.0.0']]]
            }
        }
    }
}