pipeline {
   agent any
	environment {
       PATH = "/opt/maven/bin:$PATH"
}
	stages {
      stage('Git Checkout') {
         steps {
            git 'https://github.com/saidevmalik/mediclaim.git'
		}
	}
		stage('Build') {
		steps {
			withSonarQubeEnv('sonar') {
				sh '/opt/maven/bin/mvn sonar:sonar'
			}
		}
	}
		stage("Quality Gate") {
            steps {
              timeout(time: 2, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
              }
            }
          }

}
}
