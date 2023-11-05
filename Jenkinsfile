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
                post {  
                    failure {
                            slackSend color: "danger", channel: '#jenkins-alerts',
                             message: "Pipeline failed in stage 'Package Maven'",
                             teamDomain: 'devneysho', tokenCredentialId: 'slack-alert'
                     }
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
             post {  
                    failure {
                            slackSend color: "danger", channel: '#jenkins-alerts',
                             message: "Pipeline failed in stage 'SonarQube'",
                             teamDomain: 'devneysho', tokenCredentialId: 'slack-alert'
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
             post {  
                    failure {
                            slackSend color: "danger", channel: '#jenkins-alerts',
                             message: "Pipeline failed in stage 'Nexus'",
                             teamDomain: 'devneysho', tokenCredentialId: 'slack-alert'
                     }
                }
            }
             stage('docker build'){
                 steps{
                         sh ''' ls
                                docker build -t neysho/achat-backend:1 .
                         '''
               }
               post {  
                    failure {
                            slackSend color: "danger", channel: '#jenkins-alerts',
                             message: "Pipeline failed in stage 'Docker Build'",
                             teamDomain: 'devneysho', tokenCredentialId: 'slack-alert'
                     }
                }
             }
             stage('docker push'){
                steps{
                    // sh ''' echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    //             docker push neysho/achat-backend:1
                    // '''
                    sh 'ls'
                }
                post {  
                    failure {
                            slackSend color: "danger", channel: '#jenkins-alerts',
                             message: "Pipeline failed in stage 'Docker Push'",
                             teamDomain: 'devneysho', tokenCredentialId: 'slack-alert'
                     }
                }
             }
             stage('Docker compose'){
                steps{
                    sh 'docker compose up -d'
                }
                post {  
                    failure {
                            slackSend color: "danger", channel: '#jenkins-alerts',
                             message: "Pipeline failed in stage 'Docker compose'",
                             teamDomain: 'devneysho', tokenCredentialId: 'slack-alert'
                     }
                }
            }
            stage('Trivy Scan'){
                steps{
                    sh 'trivy image --scanners vuln neysho/achat-backend:1  --timeout 35m > backend-scan.txt'
                    slackUploadFile filePath: 'backend-scan.txt', initialComment: 'Trivy Scan :'
                }
                post { 
                    failure {
                            slackSend color: "danger", channel: '#jenkins-alerts',
                             message: "Pipeline failed in stage 'Trivy Scan'",
                             teamDomain: 'devneysho', tokenCredentialId: 'slack-alert'
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
               success {
                    slackSend color: "good", channel: '#jenkins-alerts', message: 'Pipeline completed successfully!',
                     teamDomain: 'devneysho', tokenCredentialId: 'slack-alert'
              }  
               failure {
                    slackSend color: "warning", channel: '#jenkins-alerts', message: 'Check logs.',
                     teamDomain: 'devneysho', tokenCredentialId: 'slack-alert'
             }
         }

}
