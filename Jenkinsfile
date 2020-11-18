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
		stage('unit-tests') {
            steps {
                sh 'mvn test -Pcoverage'
                stash includes: 'src/**, pom.xml, target/**', name: 'unit'
                //save because of coverage re usage in 'it-tests' stage
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
		stage("email"){
			steps{
		mail bcc: '', body: 'Build is sucessful', cc: '', from: '', replyTo: '', subject: 'Build', to: 'saidevmalik123@gmail.com'
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
		//stage ('DB Migration') {
		//steps {
		//sh '/opt/maven3/bin/mvn clean flyway:migrate'
		//}
	//}
		
		post {
            success {
                echo "Test run completed succesfully."
            }
            failure {
                echo "Test run failed."
            }
            always {
        // Let's wipe out the workspace before we finish!
                deleteDir()
                echo "Workspace cleaned"
            }
    }
	}
}
		
