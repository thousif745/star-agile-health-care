pipeline {
  agent any
     tools {
       maven 'M2_HOME'
           }
     
  stages {
    stage('Git Checkout') {
      steps {
        echo 'This stage is to clone the repo from github'
        git branch: 'master', url: 'https://github.com/thousif745/star-agile-health-care.git'
                        }
            }
    stage('Create Package') {
      steps {
        echo 'This stage will compile, test, package my application'
        sh 'mvn package'
                          }
            }
    stage('Generate Test Report') {
      steps {
        echo 'Checking TestNG report generation'
        sh 'ls -l /var/lib/jenkins/workspace/healthcare/target/surefire-reports'  // Check if report exists
        
        echo 'Generating test report using TestNG'
        publishHTML([
          allowMissing: false, 
          alwaysLinkToLastBuild: false, 
          keepAll: false, 
          reportDir: '/var/lib/jenkins/workspace/healthcare/target/surefire-reports', 
          reportFiles: 'index.html', 
          reportName: 'HTML Report', 
          reportTitles: ''
        ])
      }
    }
     stage('Login to Dockerhub') {
      steps {
             withCredentials([usernamePassword(credentialsId: 'dockeruser', passwordVariable: 'password', usernameVariable: 'username')]) {
          // withCredentials([usernameColonPassword(credentialsId: 'docker-id-user', variable: 'docker-all')]) {
          // withCredentials([string(credentialsId: 'dockercode', variable: 'dockervarcode')]) {
           sh 'docker login -u thousifahamed -p ${password}'
            }
         }
     }
    stage('Docker Push-Image') {
      steps {
        echo 'This stage will push my new image to the dockerhub'
        sh 'docker push thousifahamed/healthcare:latest'
            }
      }
   stage('Deploying to Kubernetes with Ansible') {
  steps {
    echo 'Deploying application to Kubernetes cluster using Ansible'
    sh """
      ansible-playbook -i /etc/ansible/hosts ansible-playbook.yml -e ansible_ssh_common_args='-o StrictHostKeyChecking=no'
    """
    sh 'kubectl apply -f deploy.yml'
  }
}
    
  //  stage('Deploying to Kubernetes with Ansible') {
   //   steps {
   //     echo 'Deploying application to Kubernetes cluster using Ansible'
   //     sh 'ansible-playbook -i /etc/ansible/hosts ansible-playbook.yml'
   //     sh 'kubectl apply -f deploy.yml'  // Apply the Kubernetes manifest after Ansible deployment
   //   }
   // }
  }
}  
      
  
 
