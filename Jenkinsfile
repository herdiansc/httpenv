pipeline {
  environment {
    containerRegistry = "registry:5000"
    serviceAuthor = "herdiansc"
    serviceName = "httpenv"
  }
  agent any
  stages {
    stage("Checkout") {
      steps {
        // Clone configuration reposository into "cbconfig" directory.
        checkout scm: [
            $class: 'GitSCM',
            userRemoteConfigs: [
              [
                url: "https://github.com/${serviceAuthor}/${serviceName}",
              ]
            ],
            branches: [
              [
                name: "*/${branchName}"
              ]
            ],
            extensions: [
              [
                $class: 'RelativeTargetDirectory',
                relativeTargetDir: 'src'
              ]
            ]
          ],
          poll: false
      }
    }
    stage('Building Image') {
      steps {
        sh 'echo "Building image from branch ${branchName}"'
        dir('src') {
          script {
            dockerImage = docker.build "${serviceAuthor}/${serviceName}"
          }
        }
      }
    }
    stage('Pushing Image') {
      steps {
        script {
          docker.withRegistry("http://${containerRegistry}") {
            dockerImage.push("latest")
          }
        }
      }
    }
    stage('Deploy') {
      steps {
        sshagent(credentials: ['multipass-node1']) {
          sh "ssh -o StrictHostKeyChecking=no ubuntu@10.110.96.165 docker service rm ${serviceName}"
          sh "ssh -o StrictHostKeyChecking=no ubuntu@10.110.96.165 docker service create --name ${serviceName} --replicas 2 -p 8888:8888 192.168.234.17:5000/${serviceAuthor}/${serviceName}"
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
