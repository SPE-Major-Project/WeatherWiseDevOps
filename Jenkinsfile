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
        stage('Checkout Frontend') {
            steps {
                dir('frontend') {
                    git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/SPE-Major-Project/WeatherWiseFrontend.git'
                }
            }
        }
        
        stage('Build Frontend'){
            steps{
                dir('frontend') {
                    sh 'npm install && npm run build'
                }
            }
        }
        
        stage('Checkout Backend') {
            steps {
                dir('backend') {
                    git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/SPE-Major-Project/WeatherWiseBackend.git'
                }
            }
        }
        
        stage('Build Backend'){
            steps{
                dir('backend') {
                    sh 'mvn clean install -DskipTests'
                }
            }
        }
        
        stage('Build & Tag Docker Image : Frontend') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('frontend') {
                            sh "docker build -t 48483/weatherwisefrontend:latest -f DockerFileFrontend ."
                        }
                    }
                }
            }
        }
        
        stage('Build & Tag Docker Image : Backend') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('backend'){
                            sh "docker build -t 48483/weatherwisebackend:latest -f DockerFileBackend ."
                        }
                    }
                }
            }
        }
        
        
        stage('Push Docker Image : Frontend') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push 48483/weatherwisefrontend:latest"
                    }
                }
            }
        }
        
        stage('Push Docker Image : Backend') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push 48483/weatherwisebackend:latest"
                    }
                }
            }
        }
        
        
    }
}
