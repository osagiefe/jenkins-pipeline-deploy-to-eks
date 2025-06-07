#!/usr/bin/env groovy
pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
        AWS_DEFAULT_REGION = "us-east-1"
    }
     parameters {
        
        
        choice choices: ['apply', 'destroy'], description: '''Choose your terraform action
        ''', name: 'action'
    }
    stages {
         stage("Terraform init stage"){
            steps {
                script {
                    dir('terraform') {
                       sh'terraform init'
                      
                    }
                }
            }
        }
        stage("Terraform format stage"){
            steps {
                script {
                    dir('terraform') {
                       
                       sh'terraform fmt'
                      
                    }
                }
            }
        }
        stage("Terraform validate stage"){
            steps {
                script {
                    dir('terraform') {
                      
                       sh'terraform validate'
                    }
                }
            }
        }
        stage('Previewing the infrastructure'){
            steps{
                script{
                    dir('terraform'){
                         sh 'terraform plan'
                    }
                    input(message: "Approve?", ok: "proceed")
                }
            }
        }
        stage('Create/Destroy an EKS cluster'){
            steps{
                script{
                    dir('terraform'){
                         sh 'terraform $action --auto-approve'
                    }
                }
            }
        }
        stage('Deploying to Kubernetes') {
          steps {
            script {
            dir('kubernetes') {
              sh "aws eks update-kubeconfig --name myapp-eks-cluster"
              sh 'kubectl config current-context'
                      
              sh "kubectl get ns"
              sh "kubectl apply -f nginx-deployment.yaml"
              sh "kubectl apply -f nginx-service.yaml"

          }
        }
      }
    }

  }
}
    