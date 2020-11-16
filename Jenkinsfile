pipeline {
   agent any
	environment {
       PATH = "/opt/maven3/bin:$PATH"
}
	stages {
      stage('Git Checkout') {
         steps {
            git 'https://github.com/saidevmalik/mediclaim.git'
		}
	}
		stage ('Build'){
       steps {
             sh 'mvn clean install'
}
}
		stage('emailnotification'){
    steps{
    emailext body: 'test', subject: 'jobrun', to: 'saidevmalik123@gmail.com'
    }
}
		stage('SonarAnalysis')

    {steps {

       withSonarQubeEnv('sonar') {

           sh 'mvn sonar:sonar'   
      }
    }
 }
 stage('Publish Test Coverage Report') {
   steps {
      step([$class: 'JacocoPublisher', 
           execPattern: '**/build/jacoco/*.exec',
           classPattern: '**/build/classes',
           sourcePattern: 'src/main/java',
           exclusionPattern: 'src/test*'
           ])
          }
      }
	stage('Build') {
		steps {
			withSonarQubeEnv('sonar') {
				sh '/opt/maven/bin/mvn clean verify sonar:sonar'
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
	stage ('Deploy') {
		steps {
			sh '/opt/maven/bin/mvn clean deploy -Dmaven.test.skip=true'
		}
	}
}
}
