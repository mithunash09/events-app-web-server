// prerequisites: a nodejs app must be deployed inside a kubernetes cluster
// TODO: look for all instances of [] and replace all instances of 
//       the 'variables' with actual values 
// variables:
//      https://github.com/mithunash09/events-app-web-server.git
//      main
//      dtc-092021-u419
//      web-server-image
//      cluster-roi-1
//      us-central1-a
//      the following values can be found in the yaml:
//      demo-ui
//      demo-ui (in the template/spec section of the deployment)

pipeline {
    agent any 
    stages {
        stage('Stage 1') {
            steps {
                echo 'Retrieving source from github' 
                git branch: 'main',
                    url: 'https://github.com/mithunash09/events-app-web-server.git'
                echo 'Did we get the source?' 
                sh 'ls -a'
            }
        }
        stage('Stage 2') {
            steps {
                echo 'workspace and versions' 
                sh 'echo $WORKSPACE'
                sh 'gcloud version'
                sh 'node -v'
                sh 'npm -v'
        
            }
        }        
         stage('Stage 3') {
            environment {
                PORT = 8081
            }
            steps {
                echo 'install dependencies' 
                sh 'npm install'
                echo 'Run tests'
                sh 'npm test'
        
            }
        }        
         stage('Stage 4') {
            steps {
                echo "build id = ${env.BUILD_ID}"
                echo 'Tests passed on to build Docker container'
                sh "gcloud builds submit -t gcr.io/dtc-092021-u419/web-server-image:v2.${env.BUILD_ID} ."
            }
        }        
         stage('Stage 5') {
            steps {
                echo 'Get cluster credentials'
                sh 'gcloud container clusters get-credentials cluster-roi-1--zone us-central1-a --project dtc-092021-u419'
                echo 'Update the image'
                echo "gcr.io/dtc-092021-u419/web-server-image:2.${env.BUILD_ID}"
                sh "kubectl set image deployment/demo-ui demo-ui=gcr.io/dtc-092021-u419/web-server-image:v2.${env.BUILD_ID} --record"
            }
        }
    }
}

