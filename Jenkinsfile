pipeline {
  agent any
  tools { 
        maven 'Maven_3_8_4'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=isolis-panw_devsecops-jenkins-k8s-tf-sast-sonarcloud-repo -Dsonar.organization=isolis-panw -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=60dea6f8dfbf3b065b73272f2505ac84c2996bb5'
			}
    }

	stage('RunSCAAnalysisUsingSnyk') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }

	stage('Build') { 
            steps { 
               withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                 script{
                 app =  docker.build("asg")
                 }
               }
            }
    }

	stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://585217285269.dkr.ecr.us-east-2.amazonaws.com', 'ecr:us-east-2:aws-ecr-role') {
                    app.push("latest")
                    }
                }
            }
    	}
	    
  }
}
