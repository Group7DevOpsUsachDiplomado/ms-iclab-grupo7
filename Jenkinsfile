def GIT_COMMIT_USERNAME

pipeline {
    agent any
    tools 
    {
        maven 'maven_env'
    }
    parameters
    {
        choice(name: 'Build_Version', choices: ['Patch','Minor','Mayor'], description: 'Seleccion de tipo de versionamiento')
        booleanParam(name: 'Release Version' , defaultValue: false , description: 'Enviar hacia el Repositorio Nexus y realiza versionamiento')
    }
    stages {
        stage("Env Variables") {
            steps {
                sh "printenv"
            }
        }
        stage(' Compile Code ') {
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
                cleanWs()
            }
        }
    }
    post
        {
        success
        {
            slackSend channel: 'C045DSH239N', color: '#17FF00', message: "Build Success: ${GIT_COMMIT_USERNAME} ${env.JOB_NAME} ${params.Build_Tool} Ejecucion Exitosa"
        }
        failure
        {
            GIT_COMMIT_USERNAME = sh (script: 'git show -s --pretty=%an', returnStdout: true ).trim()
            slackSend channel: 'C045DSH239N', color: '#FF0000', message: "Build Fallido: ${GIT_COMMIT_USERNAME} ${env.JOB_NAME} ${params.Build_Tool} Ejecucion Fallida (<${env.BUILD_URL}|Open>)"
        }

        }
}