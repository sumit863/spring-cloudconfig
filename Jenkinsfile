pipeline {
	agent any
	environment {
		mavenhome = tool 'myMaven'
		dockerhome = tool 'myDocker'
		PATH = "$dockerhome/bin:$mavenhome/bin:$PATH"
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

		stage('Package && SonarQube Analysis') {
			steps {
				withSonarQubeEnv('My SonarQube Server') {
                    // Optionally use a Maven environment you've configured already
                    withMaven(maven:'myMaven') {
                        sh 'mvn clean package sonar:sonar'
                    }
                }
				//sh "mvn package -DskipTests"
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

		// stage('Sonar Scanner') {
		// 	steps {
		// 		def sonarqubeScannerHome = tool name: 'sonar', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
		// 		withCredentials([string(credentialsId: 'sonar', variable: 'sonarlogin')]) {
		// 			sh "${sonarqubeScannerHome}/bin/sonar-scanner -e -Dsonar.host.url=http://sonarqube:9000 -Dsonar.login=${sonarLogin} -Dsonar.projectName=sum-jenkins-ms -Dsonar.projectVersion=${env.BUILD_NUMBER} -Dsonar.projectKey=SJM -Dsonar.sources=src/main/ -Dsonar.tests=src/test/ -Dsonar.language=java -Dsonar.java.binaries=."
		// 		}
		// 	}
		// }

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
