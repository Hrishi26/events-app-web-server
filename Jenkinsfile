// prerequisites: a nodejs app must be deployed inside a kubernetes cluster
// TODO: look for all instances of [] and replace all instances of 
//       '[VARIABLE]' with actual values 
//        e.g https://github.com/Hrishi26/events-app-web-server.git might become https://github.com/MyName/external.git
// variables:
//      https://github.com/Hrishi26/events-app-web-server.git
//      main
//      dtc-jan21-u112
//      web-server-image
//      cnd-cluster 
//      us-central1-c
//      the following values can be found in the yaml:
//      demo-ui
//      demo-ui (name of the container to be replaced - in the template/spec section of the deployment)


pipeline {
    agent none 
    stages {
        stage('Get Source') {
             agent {
                docker { 
                    image 'node:14-alpine'
                    args '-e HOME=/tmp -e NPM_CONFIG_PREFIX=/tmp/.npm'
                }
            }
            steps {
                echo 'Retrieving source from github' 
                git branch: 'main',
                    url: 'https://github.com/Hrishi26/events-app-web-server.git'
                echo 'Did we get the source?' 
                sh 'ls -a'
            }
        }
        stage('Dependencies') {
             agent {
                docker {
                    image 'node:14-alpine'
                    args '-e HOME=/tmp -e NPM_CONFIG_PREFIX=/tmp/.npm'
                }
            }
            steps {
                echo 'install dependencies' 
                sh 'npm install'
                echo 'Dependencies installed on to run Tests'
            }
        }
        stage('Run Tests') {
             agent {
                docker {
                    image 'node:14-alpine'
                    args '-e HOME=/tmp -e NPM_CONFIG_PREFIX=/tmp/.npm'
                }
            }
            steps {
                echo 'Run tests'
                sh 'npm test'
                echo 'Tests passed on to build and deploy Docker container'
            }
        }
         stage('Build and deploy the container') {
             agent {
                docker { 
                    image 'google/cloud-sdk:latest'
                    args '-e HOME=/tmp'
                        }
                    }
            steps {
                echo "submit gcr.io/dtc-jan21-u112/web-server-image:v2.${env.BUILD_ID}"
                sh "gcloud builds submit -t gcr.io/dtc-jan21-u112/web-server-image:v2.${env.BUILD_ID} ."
                echo 'Get cluster credentials'
                sh 'gcloud container clusters get-credentials cnd-cluster --zone us-central1-c --project dtc-jan21-u112'
                echo "Update the image to use gcr.io/dtc-jan21-u112/web-server-image:v2.${env.BUILD_ID}"
                sh "kubectl set image deployment/demo-ui demo-ui=gcr.io/dtc-jan21-u112/web-server-image:v2.${env.BUILD_ID} --record"
            }
        }            
    }
}
