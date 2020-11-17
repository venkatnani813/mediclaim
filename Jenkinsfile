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
		stage('emailnotification'){
    steps{
    emailext body: 'test', subject: 'jobrun', to: 'saidevmalik123@gmail.com'
    }
}
		stage ('Build'){
       steps {
             sh 'mvn clean install'
}
}
	stage('SonarAnalysis')

    {steps {

       withSonarQubeEnv('sonar-3') {

           sh 'mvn sonar:sonar'   
      }
    }
 }
		//stage("Quality Gate") {
  //steps {
    //timeout(time: 1, unit: 'MINUTES') {
        //waitForQualityGate abortPipeline: true
   // }
 // }
//}
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
	
}
}
