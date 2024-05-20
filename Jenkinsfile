pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        maven 'maven3'
        nodejs 'npm'
    }
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }
    
    stages {
        stage('Checkout Code') {
            steps {
                dir('frontend') {
                    git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/SPE-Major-Project/WeatherWiseFrontend.git'
                }
                dir('backend') {
                    git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/SPE-Major-Project/WeatherWiseBackend.git'
                }
                dir('devops') {
                    git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/SPE-Major-Project/WeatherWiseDevOps.git'
                }
            }
        }
        
        stage('Build Code'){
            steps{
                dir('frontend') {
                    sh 'npm run build'
                }
                dir('backend') {
                    sh 'mvn clean install -DskipTests'
                }
            }
        }
          
        stage('Build Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('devops') {
                            sh "docker build -t 48483/weatherwisefrontend:latest -f DockerFileFrontend ."
                            sh "docker build -t 48483/weatherwisebackend:latest -f DockerFileBackend ."
                        }
                    }
                }
            }
        }
                
        stage('Push Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push 48483/weatherwisefrontend:latest"
                        sh "docker push 48483/weatherwisebackend:latest"
                    }
                }
            }
        }    
    }
}
