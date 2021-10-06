pipeline {
	agent any
	environment {
		mavenhome = tool 'myMaven'
		dockerhome = tool 'myDocker'
		sonarhome = tool 'sonar'
		PATH = "$sonarhome/bin:$dockerhome/bin:$mavenhome/bin:$PATH"
	}

	stages {
		stage('Checkout') {
			steps{
				echo "Build"
				echo "Name - $env.JOB_NAME"
			}	
		}

		// stage('Compile') {
		// 	steps {
		// 		sh "mvn clean compile"
		// 	}
		// }

		// stage('Test') {
		// 	steps {
		// 		echo "Test"
		// 		sh "mvn test"
		// 	}
		// }

		// stage('Integration Test') {
		// 	steps {
		// 		sh "mvn failsafe:integration-test failsafe:verify"
		// 	}
		// }

		stage('Package') {
			steps {
				sh "mvn package -DskipTests"
			}
		}

		stage('Build Docker Image') {
			steps {
				//"docker build -t in28min/currency-exchange-devops:$env.BUILD_TAG"
				sh 'docker version'
				script {
					echo 'Inside script'
					dockerImage = docker.build("sumitrajput/currency-exchange-devops:${env.BUILD_TAG}")
					echo 'Image built'
				}

			}
		}

		stage('Sonar Scanner') {
			steps {
				withCredentials([string(credentialsId: 'sonar', variable: 'sonarlogin')]) {
					sh "/sonar-scanner -e -Dsonar.host.url=http://sonarqube:9000 -Dsonar.login=${sonarLogin} -Dsonar.projectName=sum-jenkins-ms -Dsonar.projectVersion=${env.BUILD_NUMBER} -Dsonar.projectKey=GS -Dsonar.sources=complete/src/main/ -Dsonar.tests=complete/src/test/ -Dsonar.language=java -Dsonar.java.binaries=."
				}
			}
		}

		stage('Push Docker Image') {
			steps {
				script {
					docker.withRegistry('', 'dockerhub') {
						dockerImage.push();
						dockerImage.push('latest');
					}
				}
			}
		}
	}
	post {
		always {
			echo "I ran"
		}

		success {
			echo "Run Successful"
		}

		failure {
			echo "Build failed"
		}
	}
}
