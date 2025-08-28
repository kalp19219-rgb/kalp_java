pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
                script {
                    git branch: 'main', url: 'https://github.com/kalp19219-rgb/kalp_java.git'
                }
            }
        }
    }
}
