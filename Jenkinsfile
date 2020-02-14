pipeline {
    agent any
    environment{

        dockerImage = ''

        registryurl = 'muralidhar123/dbmysql'

        dockercred = 'dockerhub' 
    }

    stages {
        stage("scm using git"){
        
            steps {
                git url: 'https://github.com/satyamuralidhar/mysql-docker-pipeline.git'
            }
        }
           
        stage('Building image') {
            steps{
                script {
                    dockerImage = docker.build registryurl + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Deploy Image') {
            steps{
                script {
                    docker.withRegistry( '', dockercred ) {
                        dockerImage.push()
                    }   
                }
            }
        }
        stage('Remove Unused docker image') {
            steps{
                sh "docker rmi $registryurl:$BUILD_NUMBER"
            }
        }
        stage("git repo") {
            steps {
                sh "rm -rf mysql-gitrepo && mkdir mysql-gitrepo"
                    dir('mysql-gitrepo') {
                        sh 'git clone https://github.com/satyamuralidhar/mysql-helm.git'
                        sh 'cd mysql-helm'
                        sh 'helm install mysql --generate-name'
                            dir('mysql') {
                                dir('templates') {
                                sh "sed -i '/tag/s/:.*\$/: ${BUILD_NUMBER}/g' values.yaml"
                            
                                }
                            }    
                    }
               
                
                    
                
                
            }
        }
    }
       
    // slack notification  
    post {
        success {
            slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")

        }
        failure {
            slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        } 
    }        
         
} 
