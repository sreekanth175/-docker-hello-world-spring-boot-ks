node {
    // reference to maven
    // ** NOTE: This 'maven-3.6.1' Maven tool must be configured in the Jenkins Global Configuration.   
    def mvnHome = tool 'maven-3.6.1'

    // holds reference to docker image
    def dockerImage
    // ip address of the docker private repository(nexus)
    
    //def dockerRepoUrl = "localhost:8083"
    //def dockerRepoUrl = "https://hub.docker.com/repository/docker/knowledgeshare99/docker-hello-world-spring-boot-ks"
    def dockerImageName = "hello-world-java"
    //def dockerImageTag = "${dockerRepoUrl}/${dockerImageName}:${env.BUILD_NUMBER}"
    def dockerImageTag = "${dockerImageName}:${env.BUILD_NUMBER}"
    
    stage('Clone Repo') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/knowledgeshare99/docker-hello-world-spring-boot-ks.git'
      // Get the Maven tool.
      // ** NOTE: This 'maven-3.6.1' Maven tool must be configured
      // **       in the global configuration.           
      mvnHome = tool 'maven-3.6.1'
    }    
  
    stage('Build Project') {
      // build project via maven
      sh "'${mvnHome}/bin/mvn' clean package -Dmaven.test.failure.ignore"
    }
	
    stage('Publish Tests Results'){
      parallel(
        publishJunitTestsResultsToJenkins: {
          echo "Publish junit Tests Results"
		  junit '**/target/surefire-reports/TEST-*.xml'
		  archive 'target/*.jar'
        },
        publishJunitTestsResultsToSonar: {
          echo "This is branch master branch"
      })
    }
		
    stage('Build Docker Image') {
      // build docker image
      sh "whoami"
      //sh "sudo ls -all /var/run/docker.sock"
      sh "mv ./target/hello*.jar ./data" 
      
      dockerImage = docker.build("hello-world-java")
    }
	
    stage('Run Docker Image'){
      
      // Run docker image in your local

      echo "Docker Image Tag Name: ${dockerImageTag}"

      //sh "docker login -u admin -p admin123 ${dockerRepoUrl}"
      //sh "docker login -u knowledgeshare99 -p #123 ${dockerRepoUrl}"
      
      //sh "docker run -t -p 8001:8080 ${dockerImageTag}:latest"
      sh "docker run -d -p 8003:8080 ${dockerImageName}:latest"
	    
    }
   
    stage('Deploy Docker Image'){
      
      //deploy docker image to nexus

      echo "Docker Image Tag Name: ${dockerImageTag}"

      //sh "docker login -u admin -p admin123 ${dockerRepoUrl}"
      //sh "docker login -u knowledgeshare99 -p #123 ${dockerRepoUrl}"
     sh "docker login -u knowledgeshare99 -p class@123"
     sh "docker tag ${dockerImageName} knowledgeshare99/${dockerImageTag}"
     sh "docker push knowledgeshare99/${dockerImageTag}"
    }
}
