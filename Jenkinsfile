pipeline {
   agent any
   
    environment {
	   scannerHome = tool name: 'sonar_scanner_dotnet'
	   registry = 'utkarshgoyal/samplepipeline'
	   properties = null
	   docker_port = null
	   username = 'utkarshgoyal'
	}
	
	options {
	  // prepend all console output generated during statges  
	     timestamps()
	  
	  // Set Timeout period for pipeline run after which jenkins shout abort
	     timeout(time:1, unit: 'HOURS')
	
      // Skip checking out code from default 
           skipDefaultCheckout()

       buildDiscarder(logRotator(
	     // number of build logs to keep
		 numToKeepStr: '3',
		 // history to keep in days
		 daysToKeepStr: '15'
	   ))		   	  
	}
	
	stages {
	   
	   stage('Start') { 
	      Steps {
		       checkout scm	   
			   script {
			      docker_port = 7100
			   }
		  }
	   }
	   
	   stage('nuget restore'){
	     steps {
		  // echo "Running build ${JOB_NAME} # ${BUILD_NUMBER} for ${properties['user.employeeid']} with docker as ${docker_port}"
		   echo "Nuget Restore Step"
		   bat "dotnet restore"
		 }
	   }
}
