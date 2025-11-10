pipeline {
    agent any 
    
    tools {
        maven 'myMaven' 
        jdk 'jdk8' 
    }

    environment{
        dockerHome = tool 'myDocker'
    }
    
    stages {
        stage('Weryfikacja Narzędzi'){
            steps{
                sh 'mvn --version'
                sh 'docker --version'
                echo "Zaczynamy kompilację"
                echo "Numer kompilacji = $env.BUILD_NUMBER"
                echo "Nazwa Zadania = $env.JOB_NAME"
            }
        }
        
        stage('Kompilacja'){
            steps{
                sh "mvn clean compile"
            }
        }

        stage('Testy Jednostkowe'){
            steps{
                sh "mvn test"
            }
        }
        
        stage('Testy Integracyjne'){
            steps{
                sh "mvn failsafe:integration-test failsafe:verify"
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