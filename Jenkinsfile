node{
   stage('Checkout'){
      git 'https://github.com/rajnikhattarrsinha/java-tomcat-maven-example'
   }
   stage('Build'){
      // Get maven home path and build
      def mvnHome =  tool name: 'Maven 3.5.4', type: 'maven'   
      sh "${mvnHome}/bin/mvn install"
   }
   
   stage ('Test'){
      def mvnHome =  tool name: 'Maven 3.5.4', type: 'maven'    
      sh "${mvnHome}/bin/mvn verify; sleep 3"
   }  
   
   stage('Build Docker Image'){
   sh 'docker build -t rajnikhattarrsinha/javatomcatmaven:2.0.0 .'
   }  
   
   stage('Publish Docker Image')
   {
      withCredentials([string(credentialsId: 'dockerpwd', variable: 'dockerPWD')]) {
           sh "docker login -u rajnikhattarrsinha -p ${dockerPWD}"
         }
      sh 'docker push rajnikhattarrsinha/javatomcatmaven:2.0.0'
   }
   
   stage('Pull Docker Image and Deploy'){
      def dockerRun= 'sudo docker run -p 8080:8080 -d --name javatomcatmaven-$BUILD_NUMBER rajnikhattarrsinha/javatomcatmaven:2.0.0'
      sshagent(['dockerdeployserver2']) {
        sh "ssh -o StrictHostKeyChecking=no ubuntu@34.239.128.128 ${dockerRun}"         
      }
   }   
}


