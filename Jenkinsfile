node('slave'){
   stage('SCM Checkout'){
       git credentialsId: 'rcaws22', url: 'https://github.com/rcaws22/my-app'
   }
   stage('Mvn Package'){
     def mvnHome = tool name: 'maven-3', type: 'maven'
     def mvnCMD = "${mvnHome}/bin/mvn"
     sh "${mvnCMD} clean package"
   }
   stage('Build Docker Image'){
     sh 'docker build -t rcaws22/my-app:2.0.0 .'
   }
   stage('Push Docker Image'){
     withCredentials([string(credentialsId: 'docker-pwd', variable: 'dockerHubPwd')]) {
        sh 'docker login -u rcaws22 -p ${dockerHubPwd}'
     }
     sh 'docker push rcaws22/my-app:2.0.0'
   }
   stage('Run Container on Dev Server'){
     def dockerRun = 'docker run -p 8080:8080 -d --name my-app rcaws22/my-app:2.0.0'
     sshagent(['Jenkinsmasterprivatkey']) {
       sh "ssh -o StrictHostKeyChecking=no jenkins@34.171.218.193 ${dockerRun}"
     }
   }
}
