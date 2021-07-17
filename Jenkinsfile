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
                withSonarQubeEnv('sonarqube') {
                    // sh "dotnet test /var/jenkins_home/workspace/pipelines/SampleWebApp/SampleWebApp.sln /p:CollectCoverage=true /p:CoverletOutputFormat=opencover"                    
                    sh "dotnet test /var/jenkins_home/workspace/pipelines/SampleWebApp/SampleWebApp.API.csproj -l trx -r /var/jenkins_home/workspace/pipelines/results/ /p:CollectCoverage=true /p:CoverletOutput=/var/jenkins_home/workspace/pipelines/results/coverage"
                    sh "dotnet test /var/jenkins_home/workspace/pipelines/SampleWebApp/SampleWebApp.sln -l trx -r /var/jenkins_home/workspace/pipelines/results/ /p:CollectCoverage=true /p:CoverletOutput=/var/jenkins_home/workspace/pipelines/results/coverage/ /p:MergeWith='/var/jenkins_home/workspace/pipelines/results/coverage/coverage.json'"
                    sh "dotnet test /var/jenkins_home/workspace/pipelines/SampleWebApp/SampleWebApp.sln -l trx -r /var/jenkins_home/workspace/pipelines/results/ /p:CollectCoverage=true /p:CoverletOutputFormat=opencover /p:CoverletOutput=/var/jenkins_home/workspace/pipelines/results/ /p:MergeWith='/var/jenkins_home/workspace/pipelines/results/coverage/coverage.json'"
                    sh "dotnet sonarscanner begin /k:"Sample" /d:sonar.host.url="http://localhost:9000"  /d:sonar.login="abeeab84dd86c7b4ce0e4e1672153693f57e2595"  /d:sonar.cs.opencover.reportsPaths=\"/var/jenkins_home/workspace/pipelines/results/coverage.opencover.xml\""
                    sh "dotnet  build  /var/jenkins_home/workspace/pipelines/SampleWebApp/SampleWebApp.sln"
                    sh "dotnet sonarscanner end /d:sonar.login=admin /d:sonar.password=password"
                }
            }
        }
        stage("Quality Gate") {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        // stage('Deploy API') {
        //      agent {                
        //         dockerfile {                    
        //             filename 'Dockerfile'           
        //         }
        //     }            
        //     steps {
        //         sh "docker build -t aspnetapp ."
        //         sh "docker run -d -p 8080:80 --name myapp aspnetapp"
        //         sh "docker run -d -p 8055:80 --name myapp aspnetapp"
        //     }
        // }


    }
}


