pipeline{
	environment {
		registry = "https://registry.hub.docker.com"
		registryCredential = 'docker_hub_login'
		dockerImage = ''
}
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
				echo "Building the docker container"
				script {
						dockerImage = docker.build("getintodevops/hellonode_${env.BUILD_NUMBER}")
						dockerImage.inside {
                        		sh 'echo $(curl localhost:8080)'
                    		}
						}
			}
		}
		stage('Uploading the docker image to docker hub'){
			steps{
				docker.withRegistry(registry, registryCredential ) {
				dockerImage.push("${env.BUILD_NUMBER}")
				dockerImage.push("latest")

			}
			}
		}
		

	}
}

