pipeline {
    agent any

    parameters {
        choice(name: 'action', choices: ['create', 'delete'], description: 'Choose create/Destroy')
        string(name: 'ImageName', defaultValue: 'javapp', description: "name of the docker build")
        string(name: 'ImageTag', defaultValue: 'v1', description: "tag of the docker build")
        string(name: 'DockerHubUser', defaultValue: 'vikashashoke', description: "DockerHub Username")
    }

    environment {
        DOCKER_CREDENTIALS = 'dockerhub-creds'   // <-- apne Jenkins me DockerHub credentials ID daalna
        SONARQUBE = 'sonarqube-server'           // <-- apne Jenkins me SonarQube server configure karna
    }

    stages {
        stage('Git Checkout') {
            when { expression { params.action == 'create' } }
            steps {
                git branch: 'main', url: 'https://github.com/vikash-kumar01/mrdevops_java_app.git'
            }
        }

        stage('Unit Test - Maven') {
            when { expression { params.action == 'create' } }
            steps {
                sh 'mvn test'
            }
        }

        stage('Integration Test - Maven') {
            when { expression { params.action == 'create' } }
            steps {
                sh 'mvn verify'
            }
        }

        stage('Static Code Analysis - SonarQube') {
            when { expression { params.action == 'create' } }
            steps {
                withSonarQubeEnv("${SONARQUBE}") {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Quality Gate') {
            when { expression { params.action == 'create' } }
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Maven Build') {
            when { expression { params.action == 'create' } }
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    sh """
                        docker build -t ${params.DockerHubUser}/${params.ImageName}:${params.ImageTag} .
                    """
                }
            }
        }

        stage('Docker Image Scan (Trivy)') {
            when { expression { params.action == 'create' } }
            steps {
                sh """
                    trivy image ${params.DockerHubUser}/${params.ImageName}:${params.ImageTag} || true
                """
            }
        }

        stage('Docker Push') {
            when { expression { params.action == 'create' } }
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${params.DockerHubUser}/${params.ImageName}:${params.ImageTag}
                    """
                }
            }
        }

        stage('Docker Cleanup') {
            when { expression { params.action == 'create' } }
            steps {
                sh "docker rmi ${params.DockerHubUser}/${params.ImageName}:${params.ImageTag} || true"
            }
        }
    }
}
