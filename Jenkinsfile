pipeline {
    agent {
        label "agent-dev"
    }
    tools {
        jdk "java17"
        maven "Maven3"
    }
    environment {
        APP_NAME = "complete-e2e-deployments"
        RELEASE = "1.0.0"
        DOCKER_USER = "abbaassahmed40"
        DOCKER_PASS = "dockersecret"
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }
    stages {
        stage("Clean Workspace") {
            steps {
                cleanWs()
            }
        }
        stage("Checkout SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/abbasahmed40/complete-E2E-Deployments'
            }
        }
        stage("Build Application") {
            steps {
                sh "mvn clean package"
            }         
        }
        stage("Test Application") {
            steps {
                sh "mvn test"
            }         
        }
        stage("Sonarqube Analysis") {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'jenkins-token') {
                        sh "mvn sonar:sonar"
                    }
                }
            }         
        }
        stage("Quality Gate") {
            steps {
                waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-token'
            }         
        }
         stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }

                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }         
        }
        
    }
}
