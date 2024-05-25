pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        maven 'maven3'
        nodejs 'npm'
    }
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
        // ANSIBLE_INVENTORY = 'inventory.ini'
        // ANSIBLE_PLAYBOOK = 'docker-compose-playbook.yml'
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
        
        // stage('Build Code'){
        //     steps{
        //         dir('frontend') {
        //             sh 'npm install && npm run build'
        //         }
        //         dir('backend') {
        //             sh 'mvn clean install -DskipTests'
        //         }
        //     }
        // }

        // stage('Test'){
        //     steps{
        //         dir('backend') {
        //             sh 'mvn test'
        //         }
        //     }
        // }

        // stage('File System Scan') {
        //     steps {
        //         sh "trivy fs --format table -o trivy-fs-report.html ."
        //     }
        // }
        
        // stage('SonarQube Analysis') {
        //     steps {
        //         withSonarQubeEnv ('sonar') {
        //             sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=WeatherWise -Dsonar.projectKey=WeatherWise \
        //                     -Dsonar.java.binaries=. '''
        //         }
        //     }
        // }
        
        // stage('Quality Gate') {
        //     steps {
        //         script {
        //             waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
        //         }
        //     }
        // }
  
        // stage('Build Docker Image') {
        //     steps {
        //         script {
        //             withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
        //                 dir('frontend') {
        //                     sh "docker build -t 48483/weatherwisefrontend:latest -f DockerFileFrontend ."
        //                 }
        //                 dir('backend') {
        //                     sh "docker build -t 48483/weatherwisebackend:latest -f DockerFileBackend ."
        //                 }
        //             }
        //         }
        //     }
        // }

        // stage('Docker Image Scan') {
        //     steps {
        //         sh "trivy image --format table -o trivy-frontend-image-report.html 48483/weatherwisefrontend:latest"
        //         sh "trivy image --format table -o trivy-backend-image-report.html 48483/weatherwisebackend:latest"
        //     }
        // }
                
        // stage('Push Image') {
        //     steps {
        //         script {
        //             withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
        //                 sh "docker push 48483/weatherwisefrontend:latest"
        //                 sh "docker push 48483/weatherwisebackend:latest"
        //             }
        //         }
        //     }
        // }  

        stage('Run Ansible Playbook') {
            steps {
                ansiblePlaybook(
                    playbook: './devops/docker-compose-playbook.yml',
                    inventory: './devops/inventory.ini'
                )
            }
        }
    }
    post {
        always {
            script {
                def jobName = env.JOB_NAME
                def buildNumber = env.BUILD_NUMBER
                def pipelineStatus = currentBuild.result ?: 'UNKNOWN'
                def bannerColor = pipelineStatus.toUpperCase() == 'SUCCESS' ? 'green' : 'red'
    
                def body = """
                    <html>
                    <body>
                    <div style="border: 4px solid ${bannerColor}; padding: 10px;">
                    <h2>${jobName} - Build ${buildNumber}</h2>
                    <div style="background-color: ${bannerColor}; padding: 10px;">
                    <h3 style="color: white;">Pipeline Status: ${pipelineStatus.toUpperCase()}</h3>
                    </div>
                    <p>Check the <a href="${BUILD_URL}">console output</a>.</p>
                    </div>
                    </body>
                    </html>
                """
    
                emailext (
                    subject: "${jobName} - Build ${buildNumber} - ${pipelineStatus.toUpperCase()}",
                    body: body,
                    to: 'rishabh.teli@iiitb.ac.in',
                    from: 'rishabhteli14@gmail.com',
                    replyTo: 'rishabhteli14@gmail.com',
                    mimeType: 'text/html',
                    attachmentsPattern: 'trivy-frontend-image-report.html'
                )
                
                emailext (
                    subject: "${jobName} - Build ${buildNumber} - ${pipelineStatus.toUpperCase()}",
                    body: body,
                    to: 'rishabhteli0014@gmail.com',
                    from: 'rishabhteli14@gmail.com',
                    replyTo: 'rishabhteli14@gmail.com',
                    mimeType: 'text/html',
                    attachmentsPattern: 'trivy-backend-image-report.html'
                )
            }
        }
    }
}
