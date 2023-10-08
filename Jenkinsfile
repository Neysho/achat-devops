pipeline {
    agent any
    tools{
        maven 'maven-3.9.3'
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

          post {
            always {
                script {
                    cleanWs()
               }
            }
            }
    }
