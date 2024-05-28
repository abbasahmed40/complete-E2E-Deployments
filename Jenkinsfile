pipeline {
    agent {
        label "agent-dev"
    }
    tools {
        jdk "java17"
        maven "Maven3"
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
    }
}
