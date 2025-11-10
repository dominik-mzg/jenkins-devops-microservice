pipeline {
    agent any 
    
    tools {
        maven 'myMaven' 
        jdk 'jdk8' 
    }

    environment{
        DOCKER_HOST = 'tcp://localhost:2375'
        DOCKER_TLS_VERIFY = '0'
        DOCKER_CERT_PATH = ''
    }
    
    stages {
        stage('Weryfikacja Narzędzi'){
            steps{
                sh 'mvn --version'
                sh 'docker --version'
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
        
        stage('Integration Test '){
            steps{
                sh "mvn failsafe:integration-test failsafe:verify"
            }
        }
        stage('Package'){
            steps{
                sh "mvn package -DskipTests"
            }
        }

        stage('Build Docker Image'){
            steps{
                sh "docker build -t dominikmzgg/currency-exchange-devops:${env.BUILD_NUMBER} ."
                sh "docker tag dominikmzgg/currency-exchange-devops:${env.BUILD_NUMBER} dominikmzgg/currency-exchange-devops:latest"
            }
        }
        stage('Push Docker Image'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USER')]) {
                    sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker push dominikmzgg/currency-exchange-devops:${env.BUILD_NUMBER}"
                    sh "docker push dominikmzgg/currency-exchange-devops:latest"
                }
            }
        }
    } 
    
    post{
        always{
            echo "Zawsze uruchomione"
        }
        success{
            echo "Uruchomione w przypadku sukcesu"
        }
        failure{
            echo "Uruchomione w przypadku błędu"
        }
    }
}