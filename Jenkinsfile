pipeline{
    agent{
        label "agent-dev"
    }
    tools{
        jdk "java17"
        maven "Maven3"
    } 
    stages{
        stage("Clean Workspace"){
            steps{
                CleanWs()
            }
        }
    }
    stages{
        stage("Checkout SCM"){
            steps{
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/abbasahmed40/complete-prod-E2E-Deployment'
            }
        }
    }
}