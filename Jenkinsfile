// - Install Using SSH Agent Plugin
// - Set credential private key 
pipeline {
  environment {
    pushContainerRegistry = "registry:5000"
    pullContainerRegistry = "192.168.234.17:5000"
    serviceAuthor = "herdiansc"
    serviceName = "httpenv"
    clusterManager = "ubuntu@10.110.96.165"
    clusterManagerCredId = "multipass-node1"
  }
  agent any
  stages {
    // Comment this stage if using Pipeline script from SCM
    // stage("Checkout") {
    //   steps {
    //     checkout scm: [
    //         $class: 'GitSCM',
    //         userRemoteConfigs: [
    //           [
    //             url: "https://github.com/${serviceAuthor}/${serviceName}",
    //           ]
    //         ],
    //         branches: [
    //           [
    //             name: "*/${branchName}"
    //           ]
    //         ],
    //       ],
    //       poll: false
    //   }
    // }
    stage('Building Image') {
      steps {
        sh 'echo "Building image from branch ${branchName}"'
        script {
          dockerImage = docker.build "${serviceAuthor}/${serviceName}"
        }
      }
    }
    stage('Pushing Image') {
      steps {
        script {
          docker.withRegistry("http://${pushContainerRegistry}") {
            dockerImage.push("latest")
          }
        }
      }
    }
    stage('Deploy') {
      steps {
        sshagent(credentials: ["${clusterManagerCredId}"]) {
          sh "ssh -o StrictHostKeyChecking=no ${clusterManager} docker service rm ${serviceName}"
          sh "ssh -o StrictHostKeyChecking=no ${clusterManager} docker service create --name ${serviceName} --replicas 2 -p 8888:8888 ${pullContainerRegistry}/${serviceAuthor}/${serviceName}"
        }
      }
    }
  }
  post {
    always {
      cleanWs()
    }
  }
}
