pipeline {
    agent any 
    
    tools {
        maven 'myMaven'
    }

    environment{
        dockerHome = tool 'myDocker'
    }
    
    stages {
        stage('Checkout'){
            steps{
                sh 'mvn --version'
                sh 'docker --version'
                echo "Build"
                echo "BUILD_NUMBER = $env.BUILD_NUMBER"
                echo "JOB_NAME = $env.JOB_NAME"
            }
        }
        
        stage('Compile'){
            steps{
                sh "mvn clean compile"
            }
        }

        stage('Test'){
            steps{
                sh "mvn test"
            }
        }
        
        stage('Integration Test'){
            steps{
                sh "mvn failsafe:integration-test failsafe:verify"
            }
        }
    } 
    
    post{
        always{
            echo "I run always"
        }
        success{
            echo "I run when successful"
        }
        failure{
            echo "I run when failed"
        }
    }
}