pipeline {
    agent any 
    
    tools {
        maven 'myMaven' 
        jdk 'jdk8' 
    }

    environment{
        dockerHome = tool 'myDocker'
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
                //docker build -t dominikmzgg/currency-exchange-devops:$env.BUILD_NUMBER
                script {
                    withEnv(["DOCKER_HOST=tcp://localhost:2375", "DOCKER_TLS_VERIFY=0", "DOCKER_CERT_PATH="]) {
                         dockerImage = docker.build("dominikmzgg/currency-exchange-devops:${env.BUILD_NUMBER}")
                }
            }
        }
        stage('Push Docker Image'){
            steps{
                script {
                    docker.withRegistry('', 'dockerhub') {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
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
}