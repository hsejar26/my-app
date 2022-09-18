node{
   stage('Download-PKG-From-GIT'){
     git 'https://github.com/hsejar26/my-app.git'
   }
   stage('Compile-PKG-With-Maven'){
      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
  stage('Push to SONAR') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
  
  stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
  }
   stage('Build Docker Image'){
   sh 'docker build -t hsejar26/myweb:0.0.2 .'
   }
   stage('Push Docker Image'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u hsejar26 -p ${dockerPassword}"
    }
   sh 'docker push hsejar26/myweb:0.0.2'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 13.233.225.30:8083"
   sh "docker tag hsejar26/myweb:0.0.2 13.233.225.30:8083/hsejar:1.0.0"
   sh 'docker push 13.233.225.30:8083/hsejar:1.0.0'
   }
      stage('Container-Deploy-From-Docker'){
   sh 'docker run -d -p 8090:8080 --name tomcattest hsejar26/myweb:0.0.2' 
   }
   }
