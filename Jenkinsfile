
pipeline {
    
	agent any

    environment {
        registry = "tsailendranath/hackathon"
        registryCredential = "dockerhublogin"
        
    }

    stages {
        
        stage('BUILD'){
            steps {
                sh 'npm install'
            
            }
            
        }

	    stage('TEST'){
            steps {
                sh 'npm test'
            }
        }

        stage('CODE ANALYSIS with SONARQUBE') {
          
		     environment {
             scannerHome = tool 'sonarscanner'
             }
        

            steps {
                withSonarQubeEnv('sonarqube1') {
                    sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=hackathon \
                    -Dsonar.projectName=hackathon-repo \
                    -Dsonar.projectVersion=1.0 \
                    -Dsonar.sources=. \
                    '''
                }
            

                timeout(time: 10, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('BUILD DOCKER IMGE') { 
            steps {
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('SCAN DOCKER IMAGE') {
            steps {
                echo "-----------SCANNING------------"
                sh "trivy image --timeout 30m --exit-code 100 ${registry}:${BUILD_NUMBER}"
                //sh "trivy image --format json --severity HIGH,CRITICAL ${registry}:${BUILD_NUMBER} > report.json"
                // script {

                //     if (sh(returnStdout: true, script: 'echo $?')) {
                //         error('Trivy scan found critical or high vulnerabilities.')
                //     }
                // }
            }
        }
        stage('PUBLISH TO REGISTRY') {
            steps {
                script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push("$BUILD_NUMBER")
                        dockerImage.push('latest')
                    }
                }
            }
        }
        stage('Remove Unused docker image') {
            steps{
                sh "docker rmi $registry:$BUILD_NUMBER"
            }
        }

        

    }
}
  environment {
    imagename = "tsailendranath/hackathon"
    ecrurl = "https://837771900128.dkr.ecr.us-east-1.amazonaws.com"
    ecrcredentials = "ecr:us-east-1:aws-creds"

  } 

stage('k8s') {
            steps {
                script {
		  dir("helm")
			{
			    withAWS(credentials: 'aws-cred', region: 'ap-south-1') {
                    script {
                    sh ('aws eks update-kubeconfig --name basic-cluster --region us-east-1')
			    sh "cd kaiburr && helm upgrade -i kaiburr . --set image.tag=${BUILD_NUMBER} --set mongodburl=mongodb://54.221.132.34:27017/test"
                }
			}	}
                    
                }
                
            }
        }	  

 

  }

  post {
     always {
	  
       cleanWs()
         
       }
    }  
}
