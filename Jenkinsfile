pipeline {
    agent any    
    stages {
        stage('Build') {
            steps('Build Class library') {	
               sh "dotnet clean SampleWebApp/SampleWebApp.sln"
               sh "dotnet restore /var/jenkins_home/workspace/pipelines/SampleWebApp/SampleWebApp.sln"
               sh "dotnet build /var/jenkins_home/workspace/pipelines/SampleWebApp/SampleWebApp.sln"                             
            }
        }
         stage('UnitTests') {
            steps {                
              	sh returnStatus: true, script: "dotnet test SampleWebApp/SampleWebApp.sln --logger \"trx;LogFileName=/var/jenkins_home/workspace/pipelines/unit_tests.xml\" --no-build /p:CollectCoverage=true /p:CoverletOutputFormat=opencover"
		        step([$class: 'MSTestPublisher', testResultsFile:"**/unit_tests.xml", failOnError: true, keepLongStdio: true])
            }
        }
        
        stage('Sonarqube') {
	     steps {
	     echo "Start sonarqube analysis step"
		 withSonarQubeEnv('Test_Sonar'){
		    bat "${scannerHome}\\SonarScanner.MSBuild.exe begin /k:Sample /n:SampleWebApp /v:1.0"
		 }
	 }
            }
        }
    }
}


