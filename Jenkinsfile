pipeline {
  agent any
  stages {
    stage("Clear Workspace"){
        steps {
          sh 'rm -rvf k*'
        }
    }
    stage("Checkout") {
        steps {
        sh 'git clone https://github.com/kovidareddy/kovida.git'
      }
    }
    stage('GCP Auth') {
        steps {
      withCredentials([usernameColonPassword(credentialsId: 'GCP_CREDENTIALS', variable: 'GCP_PROJECT'), file(credentialsId: 'service_account', variable: 'service_account')]) {
         sh 'gcloud auth activate-service-account --key-file=$service_account'
        }
      }
    }
    stage("Docker pull") {
      steps {
        withCredentials([usernameColonPassword(credentialsId: 'docker_credentials', variable: 'docker_credentials')]) {
        sh "docker login -u kovidareddy1342 -p pandu1342"
        sh 'docker pull kovidareddy1342/newmysql:sql1'
       }
     }
    }
    stage("Docker tag") {
      steps {     
         sh 'docker tag kovidareddy1342/newmysql:sql1 gcr.io/my-project2-35783/newmysql:sql2'
      }
    }
 
    stage("Docker push") { 
        steps {
           sh 'gcloud auth configure-docker'
           sh 'docker push gcr.io/my-project2-35783/newmysql:sql2'
           }
        }
     stage("cluster create") {
       steps {
          sh 'gcloud container clusters create sqlserver-cluster --num-nodes 3 --location=asia-south1-a'
       }
     }
    stage("Create & expose deploy") {
       steps {
         sh 'kubectl create deployment sqlserver --image=gcr.io/my-project2-35783/newmysql:sql2'
         sh 'kubectl expose deployment sqlserver --type=LoadBalancer --port 80 --target-port 8080'
       }
    }
  }  
}
