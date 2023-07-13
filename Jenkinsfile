pipeline {
  agent any
  stages {
    stage("Checkout") {
        steps {
        sh 'git clone https://github.com/kovidareddy/kovida.git kovida-new'
      }
    }
    stage('GCP Auth') {
        steps {
        withCredentials([usernameColonPassword(credentialsId: 'GCP_PROJECT', variable: 'GCP_PRPOJEC T'), file(credentialsId: 'GCP_CREDENTIALS', variable: 'GCP_CREDENTIALS')]) {
         sh 'gcloud auth activate-service-account --key-file=$GCP_CREDENTIALS'
        }
      }
    }
    stage("Docker pull") {
      steps {
        withCredentials([usernameColonPassword(credentialsId: 'dockerHub', variable: 'dockerHub')]) {
        sh "docker login -u kovidareddy1342 -p pandu1342"
        sh 'docker pull kovidareddy1342/newmysql:sql1'
       }
     }
    }
    stage("Docker tag") {
      steps {     
         sh 'docker tag kovidareddy1342/newmysql:sql1 gcr.io/my-project1-80213/kovidareddy1342/newmysql:sql1'
      }
    }
    stage("Docker push") { 
        steps {
             sh 'gcloud auth configure-docker'
             sh 'docker push gcr.io/my-project1-80213/kovidareddy1342/newmysql:sql1'
           }
        }
     stage("cluster create") {
       steps {
          sh 'gcloud container clusters create sqlserver-cluster --num-nodes 3 --location=asia-south1-c'
       }
     }
    stage("Create & expose deploy") {
       steps {
         sh 'kubectl create deployment sqlserver --image=gcr.io/my-project1-80213/kovidareddy1342/newmysql:sql1'
         sh 'kubectl expose deployment sqlserver --type=LoadBalancer --port 80 --target-port 8080'
       }
    }
  }  
}
