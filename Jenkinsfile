pipeline{

	agent any
	stages{

		stage('Build code'){
			steps{
				echo "Building code with gradlew"
				sh './gradlew build --no-daemon'
				archiveArtifacts artifacts: 'dist/trainSchedule.zip'
				}
			}
		stage('Build docker image'){

			steps{
				echo 'Building the docker container'
				script {
						dockerImage = docker.build('kashyapgaurav123/train-schedule')
						dockerImage.inside {
                        		sh 'echo $(curl localhost:8080)'
                    						  }
					}
			}
		}
		  stage('Push Docker Image') {
           		 when {
                		branch 'master'
            			}
           		 steps {
                		script {
                    			docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') 
					      {
                        		dockerImage.push("${env.BUILD_NUMBER}")
                        		dockerImage.push("latest")
                    				}
                			}
            			}
        		}
		stage ('DeployToProduction') {
    						when {
        						branch 'master'
   							 }
    						steps {
        						input 'Deploy to Production'
        						milestone(1)
        						withCredentials ([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
            						script {
                						sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@${env.prod_ip} \"docker pull kashyapgaurav123/train-schedule:${env.BUILD_NUMBER}\""
                						try {
                   							sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@${env.prod_ip} \"docker stop train-schedule\""
                   							sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@${env.prod_ip} \"docker rm train-schedule\""
                						    } catch (err) {
                    							echo: 'caught error: $err'
                								}
                							sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@${env.prod_ip} \"docker run --restart always --name train-schedule -p 8080:8080 -d kashyapgaurav123/train-schedule:${env.BUILD_NUMBER}\""
            							}
        							}
    							}
						}
	}
}

