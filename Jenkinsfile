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
                    docker.withRegistry('', 'dockersecret') {
                        def dockerImage = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                        dockerImage.push("${IMAGE_TAG}")
                        dockerImage.push('latest')
                    }
                }         
            }
        }
        stage("Update Deployment File") {
            environment {
                GIT_REPO_NAME = "complete-E2E-Deployments"
                GIT_USER_NAME = "abbasahmed40"
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'github', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    sh '''
                        git config user.email "abbaass.ahmed40@gmail.com"
                        git config user.name "Abbas"
                        cat kube/deployment.yaml
                        sed -i "s/replaceImageTag/${IMAGE_TAG}/g" kube/deployment.yaml
                        cat kube/deployment.yaml
                        git add kube/deployment.yaml
                        git commit -m "Update deployment image to version ${IMAGE_TAG}" || echo "No changes to commit"
                        git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                    '''
                }
            }
        }
    }
}
