pipeline {
   agent any

   environment {
     // You must set the following environment variables
     // ORGANIZATION_NAME
     // YOUR_DOCKERHUB_USERNAME (it doesn't matter if you don't have one)
     
     SERVICE_NAME = "fleetman-webapp"
     //REPOSITORY_TAG="${YOUR_DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}"
     REPOSITORY_TAG="${YOUR_DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}"
   }

   stages {
      stage('Preparation') {
         steps {
            cleanWs()
            git credentialsId: 'GitHub', url: "https://github.com/${ORGANIZATION_NAME}/${SERVICE_NAME}"
         }
      }
      stage('Build') {
         steps {
            sh 'echo No build required for Webapp.'
         }
      }

      stage('Build Image') {
         steps {
           sh 'docker image build -t ${REPOSITORY_TAG} . --network=host'
         }          
      }
      
      /*
       stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build(REPOSITORY_TAG)
                    app.inside {
                        sh 'echo Hello, World!'
                    }
                }
            }
        }
      */
      stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                        app=docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push(REPOSITORY_TAG)
                    }
                }
            }
        }
      

      stage('Deploy to Cluster') {
          steps {
            sh 'envsubst < ${WORKSPACE}/deploy.yaml | kubectl apply -f -'
          }
      }
   }
}
