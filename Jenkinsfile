pipeline {
    agent any
	tools {
        maven 'Maven 3.8.4'
        jdk 'Java 11'
    }
    stages {
        stage('Build') { 
            steps {
                sh 'mvn -B -DskipTests clean package' 
				setBuildStatus('Build', e.take(140), "FAILURE");
            }
        }
		stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
    }
}

// Updated to account for context
void setBuildStatus(String context, String message, String state) {
  context = context ?: "ci/jenkins/build-status"
  step([
      $class: "GitHubCommitStatusSetter",
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/my-org/my-repo"],
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: context],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ]);
}
