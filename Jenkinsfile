@Library('my-shared-library') _

pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
                    gitCheckout(
                        branch: "main",
                        url: "https://github.com/kalp19219-rgb/jenkins_shared_lib.git"
            )
            }
        }

        stage('Unit Test Maven') {
            
            steps {
                script {
                    
                    mvnTest()
                }
            }
        }
    }
}
