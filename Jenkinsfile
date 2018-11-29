node{
      
      stage('Checkout'){
         git 'https://github.com/mallisakiri3/java-tomcat-maven-example'
      }
  
      stage('Build'){
         // Get maven home path and build
         def mvnHome =  tool name: 'Maven 3.5.4', type: 'maven'   
         sh "${mvnHome}/bin/mvn package"
      }
      
     stage ('Test'){
         def mvnHome =  tool name: 'Maven 3.5.4', type: 'maven'    
         sh "${mvnHome}/bin/mvn verify; sleep 3"
      }  
      
    stage('Build Docker Image'){
         sh 'docker build -t mallisakiri3/javademo:2.0.0 .'
      }  
   
      stage('Publish Docker Image'){
         withCredentials([string(credentialsId: 'dockerpwd_malli', variable: 'dockerPWDmalli')]) {
              sh "docker login -u mallisakiri3 -p ${dockerPWDmalli}"
         }
        sh 'docker push mallisakiri3/javademo:2.0.0'
      }

   stage('Stop running containers'){        
         def listContainer='sudo docker ps'
         def scriptRunner='sudo ./stopscript.sh'
         def stopContainer='sudo docker stop $(docker ps -a -q)'
         sshagent(['dockerdeployserver2']) {
              sh "ssh -o StrictHostKeyChecking=no ubuntu@18.215.68.236 ${scriptRunner}"            
         }
    } 
   stage('Pull Docker Image and Deploy'){        
         
            def dockerContainerName = 'javademo-$BUILD_NUMBER'
            def dockerRun= "sudo docker run -p 8080:8080 -d --name ${dockerContainerName} mallisakiri3/javademo:2.0.0"         
            sshagent(['dockerdeployserver2']) {
              sh "ssh -o StrictHostKeyChecking=no ubuntu@18.215.68.236 ${dockerRun}"              
         }
   }
 }
