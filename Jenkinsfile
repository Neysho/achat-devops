pipeline {
    agent any
    tools{
        maven 'maven-3.9.4'
        // dockerTool 'docker'
    }
    environment{
        DOCKERHUB_CREDENTIALS=credentials('docker-hub-neysho')
        // BUILD_NUMBER = "${env.BUILD_NUMBER-SPRINGBOOT-SAMPLE}"
    }

    stages{
        stage('checkout'){
                        steps{
                        //  deleteDir()
                         checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'github-neysho', url: 'https://github.com/Neysho/achat-devops.git']])
                       }
                  }
            stage('Compile Maven'){
                steps{
                    sh 'mvn clean compile'
                }
            }

            stage('SonarQube'){
                steps {
                 script {
                     withSonarQubeEnv(credentialsId: 'sonar-id') {
                         sh "mvn sonar:sonar"
                     }
                 }
             }
            }

    }

    post {
            always {
                script {
                    cleanWs()
               }
             }
            }

}
