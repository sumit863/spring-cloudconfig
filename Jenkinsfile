pipeline {
	agent any
	environment {
		mavenhome = tool 'myMaven'
		PATH = "$mavenhome/bin:$PATH"
	}

	stages {
		stage('Checkout') {
			echo "Build"
			echo "Name - $env.JOB_NAME"
		}

		stage('Compile') {
			steps {
				sh "mvn clean compile"
			}
		}

		stage('Test') {
			echo "Test"
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
