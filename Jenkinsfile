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

       withSonarQubeEnv('sonar3') {

           sh 'mvn sonar:sonar \
  -Dsonar.projectKey=mediclaim \
  -Dsonar.host.url=http://104.211.117.31:9000 \
  -Dsonar.login=c784eaa201cb812a7f02be95550fce589fd306c9'   
      }
    }
 }
		//stage("Quality Gate") {
  //steps {
    //timeout(time: 2, unit: 'MINUTES') {
     // waitForQualityGate abortPipeline: true
    //}
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
		stage ('Deploy') {
		steps {
			sh '/opt/maven3/bin/mvn clean deploy -Dmaven.test.skip=true'
		}
	}
		stage ('Release') {
		steps {
			sh 'export JENKINS_NODE_COOKIE=dontkillme ;nohup java -jar $WORKSPACE/target/*.jar &'
		}
	}
		//stage("email"){
			//steps{
		//mail bcc: '', body: 'Build is sucessful', cc: '', from: '', replyTo: '', subject: 'Build', to: 'saidevmalik123@gmail.com'
		//}
		//}
	post {
        always {
            emailext body: "${currentBuild.currentResult}: Project Name : ${env.JOB_NAME} Build ID : ${env.BUILD_NUMBER}\n\n Approval Link :  ${env.BUILD_URL}", recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], subject: 'Test'
        }
    }
}
}
