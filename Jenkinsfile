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
       withCredentials([usernameColonPassword(credentialsId: 'GCP_CREDENTIALS', variable: 'GCP_CREDENTIALS'), file(credentialsId: '1d777d6c-cf61-4fde-afb2-9d7d3670737f', variable: 'KEY')])  {
         sh 'gcloud auth activate-service-account --key-file=$GCP_CREDENTIALS'
        }
      }
    }
    stage("Docker pull") {
      steps {
        withCredentials([usernameColonPassword(credentialsId: 'c0d51db6-420e-404b-88f1-194834cbde82', variable: 'DOCKER_CREDENTIALS')]) {
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
          sh 'gcloud container clusters create sqlserver-cluster --num-nodes 3 --location=asia-south1-c'
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
