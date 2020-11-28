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
		
		stage('Sonar Build') {
            steps {
		        script{
				    withSonarQubeEnv('sonar') {
					sh 'mvn clean verify sonar:sonar -Dmaven.test.skip=true'
					}
                }
            }
		}
		
}
}
