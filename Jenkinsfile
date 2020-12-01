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
		 stage ('Build') {
	     steps {
	     sh 'mvn clean install'
	     }
		 }
		     stage('code-quality') {
		steps {
			withSonarQubeEnv('sonar3') {
				sh 'mvn sonar:sonar'
			}
		}
	}
		stage("Quality Gate") {
            steps {
                sh 'sleep 5s'
                timeout(time: 5, unit: 'MINUTES') {
                    script {
                        def result = waitForQualityGate()
                        if (result.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${result.status}"
                        } else {
                            echo "Quality gate passed with result: ${result.status}"
                        }
                    }
                }

            }
        }
		//stage('Publish Test Coverage Report') {
          //steps {
            // step([$class: 'JacocoPublisher', 
             //execPattern: '**/build/jacoco/*.exec',
             //classPattern: '**/build/classes',
             //sourcePattern: 'src/main/java',
             //exclusionPattern: 'src/test*'
           //])
          //}
      //}	
		//stage('unit-tests') {
            //steps {
              // sh 'mvn test -Pcoverage'
               //stash includes: 'src/**, pom.xml, target/**', name: 'unit'
//}
//}
		stage("email"){
            steps{
            mail bcc: '', body: 'Build is sucessful', cc: '', from: '', replyTo: '', subject: 'Build', to: 'saidevmalik123@gmail.com'
}
}
		  stage ('Deploy') {
	     steps {
	     sh 'mvn clean deploy'
}
}
		stage ('Release') {
	     steps {
	     sh 'export JENKINS_NODE_COOKIE=dontkillme ;nohup java -jar $WORKSPACE/target/*.jar &'
}
}
		stage ('UAT Approve')  {
	steps{
            echo "Taking approval from Uat Manager"     
            timeout(time: 7, unit: 'DAYS') {
            input message: 'Do you want to deploy?', submitter: 'user1'
            }
     }
		}
		stage('Deploye-UAT'){
	   steps{
		sshagent(['tomcat']) {
	        deploy adapters: [tomcat9(credentialsId: 'tomcat-deploy', path: '', url: 'http://52.66.195.248:8080')], contextPath: '/opt/tomcat/webapps/', war: '**/*.war'
		 //sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@52.66.195.248:/opt/tomcat/webapps/'
		}
	   }
		}
}
}
