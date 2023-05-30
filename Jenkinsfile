pipeline {
    agent any
    environment {
        PROJECT_ID = 'galvanic-fort-387616'
                CLUSTER_NAME = 'cluster-jenkins-project'
                LOCATION = 'us-central1'
                CREDENTIALS_ID = 'MyFirstProject'
    }
    
    stages {
        stage('Check Git...') {
            steps{
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/DiegoRestrepo1998/loans.git']])
            }
        }
        stage('Building image...') {
            environment {
                def imageName = "dfrestrepo1998/microservicio_loans:${env.BUILD_ID}"
            }
            steps {
                script {
                    sh "docker build -t ${imageName} ."
                    }
            }
        }
        
        stage('Pushing image...') {
            environment {
                def imageName = "dfrestrepo1998/microservicio_loans:${env.BUILD_ID}"
            }
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) {
                        sh 'docker login -u dfrestrepo1998 -p ${dockerhub}'
                        sh "docker push ${imageName}"
                    }
                }
            }
        }
    
        stage('Deploying to K8s') {
            steps{
                echo "Deployment started ..."
                sh 'ls -ltr'
                sh 'pwd'
                sh "sed -i 's/pipeline:latest/pipeline:${env.BUILD_ID}/g' loans_deploy.yml"
                step([$class: 'KubernetesEngineBuilder', \
                  projectId: env.PROJECT_ID, \
                  clusterName: env.CLUSTER_NAME, \
                  location: env.LOCATION, \
                  manifestPattern: 'loans_deploy.yml', \
                  credentialsId: env.CREDENTIALS_ID, \
                  verifyDeployments: true])
                }
            }
        }    
}