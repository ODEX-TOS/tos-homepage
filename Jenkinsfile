void setBuildStatus(String message, String state) {
  step([
      $class: "GitHubCommitStatusSetter",
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/ODEX-TOS/tos-homepage"],
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ]);
}

pipeline {
    agent {
              docker {
                 image 'jojomi/hugo:latest'
                  args '-v tos_work:/output'
               }
    }
    stages {
        stage('clone') {
            steps{
                git 'https://github.com/ODEX-TOS/tos-homepage.git'
            }
        }
        stage('Build') {
            steps {
                sh 'cd src && hugo --destination="/output"'
            }
        }
        stage('copy over pandoc') {
            steps {
                sh 'mkdir -p /output/docs/'
                sh 'cd src && cp -r public/docs/doc/* /output/docs/'
            }
        }
    }
    post {
        success {
            setBuildStatus("Build succeeded", "SUCCESS");
        }
        unstable {
            setBuildStatus("Build is unstable", "UNSTABLE");
        }
        failure {
            setBuildStatus("Build failed", "FAILED");
        }
        
    }
}
