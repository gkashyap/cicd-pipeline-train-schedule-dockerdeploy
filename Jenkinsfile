pipeline{

	agent any
	stages{

		stage('Build code'){
			steps{
				echo "Building code with gradlew"
				sh './gradlew build --no-deamon'
				archiveArtifacts artifacts: 'dist/trainSchedule.zip'
				}
			}
		stage('Build docker image'){

			steps{
				echo 'Building the docker container'
				script {
						dockerImage = docker.build('kashyapgaurav123/train-schedule_${env.BUILD_NUMBER}')
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
	}
}

