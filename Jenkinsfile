pipeline {
  agent any
  tools { 
        maven 'M2_HOME'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
<<<<<<< HEAD
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=bhanuprakash678910-github_mavenproj -Dsonar.organization=bhanuprakash678910-github -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=34722bc1ba4fe069a76541b9fd5652e5d096df4f'
=======
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=buggywebapp -Dsonar.organization=buggywebapp -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=932558e169d66a8f1d1adf470b908a46156f5844'
>>>>>>> d496799ca238428690d5b50715c591ccd6c10668
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
                 app =  docker.build("test")
                 }
               }
            }
    }

	stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://526486072183.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-east-1:aws-credentials') {
                    app.push("latest")
                    }
                }
            }
    	}
	   
	stage('Kubernetes Deployment of Bugg Web Application') {
	   steps {
	      withKubeConfig([credentialsId: 'kubelogin']) {
		  sh('kubectl delete all --all -n devsecops')
		  sh ('kubectl apply -f deployment.yaml --namespace=devsecops')
		}
	      }
   	}
	   
	stage ('wait_for_testing'){
	   steps {
		   sh 'pwd; sleep 180; echo "Application Has been deployed on K8S"'
	   	}
	   }
	   
	stage('RunDASTUsingZAP') {
          steps {
		    withKubeConfig([credentialsId: 'kubelogin']) {
				sh('zap.sh -cmd -quickurl http://$(kubectl get services/buggy --namespace=devsecops -o json| jq -r ".status.loadBalancer.ingress[] | .hostname") -quickprogress -quickout ${WORKSPACE}/zap_report.html')
				archiveArtifacts artifacts: 'zap_report.html'
		    }
	     }
       } 
  }
}
