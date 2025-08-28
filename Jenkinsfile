@Library('my-shared-library') _

pipeline {
    agent any

    stages{

        stage('Git Checkout'){
            steps{
                gitCheckout(
                    branch: "main",
                    url: "https://github.com/kalp19219-rgb/kalp_java.git"
                )
            }
        }

        stage('Unit Test Maven') {
            steps{
                script{
                    mvnTest()
                }
            }
        }
    }
}
