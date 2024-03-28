pipeline {
  environment {
    dockerimagename = "herdiansc/httpenv"
    dockerImage = ""
  }
  agent any
  stages {
    stage('Initialize'){
      steps {
        script {
          def dockerHome = tool 'jenkinsDocker'
          env.PATH = "${dockerHome}/bin:${env.PATH}"
        }
        sh 'sudo usermod -aG docker jenkins'
      }
    }
    stage('Building Container') {
      steps {
        sh 'echo "Building container from branch ${branchName}"'
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }
    // stage('Build image') {
    //   steps{
    //     script {
    //       dockerImage = docker.build dockerimagename
    //     }
    //   }
    // }
    // stage('Pushing Image') {
    //   environment {
    //       registryCredential = 'dockerhub-credentials'
    //        }
    //   steps{
    //     script {
    //       docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
    //         dockerImage.push("latest")
    //       }
    //     }
    //   }
    // }
    // stage('Deploying React.js container to Kubernetes') {
    //   steps {
    //     script {
    //       kubernetesDeploy(configs: "deployment.yaml", 
    //                                      "service.yaml")
    //     }
    //   }
    // }
  }
}
