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
            stage('Package Maven'){
                steps{
                    sh 'mvn clean package'
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

            stage('Nexus'){
                steps {
                nexusArtifactUploader artifacts:
                 [[artifactId: 'achat',
                  classifier: '',
                  file: 'target/achat-app.jar',
                  type: 'jar']],
                  credentialsId: 'nexus', groupId: 'tn.esprit.rh',
                  nexusUrl: '192.168.1.100:8081',
                   nexusVersion: 'nexus3', protocol: 'http',
                    repository: 'achat-app',
                    version: '1.0'
             }
            }
             stage('docker build'){
                 steps{
                         sh ''' ls
                                docker build -t neysho/achat-backend:1 .
                         '''
               }
             }
             stage('docker push'){
                steps{
                    // sh ''' echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    //             docker push neysho/achat-backend:1
                    // '''
                }
             }
             stage('Docker compose'){
                steps{
                    sh 'docker-compose up -d'
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
