pipeline {
    agent any
    
      environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/SushantOps/Vitual-Browser.git'
            }
        }
        
        stage('OWASP Dependency') {
            steps {
                dependencyCheck additionalArguments: '--scan . / --disableNodeAudit', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
    
        stage(" Sonarqube Analysis "){
            steps{
                 withSonarQubeEnv('sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Vritual-demo \
                    -Dsonar.projectKey=Vritual-demo '''
                    
                 }
            }
        } 
        
        stage("Docker Build & Tag"){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                    
                    dir('/home/ubuntu/.jenkins/workspace/virtual_browser/.docker/firefox') {
      sh "docker build -t amuldark/virtual-demo:latest ."
         }
                     }
                }
            }
        }
        
        stage("TRIVY"){
            steps{
                sh "trivy image amuldark/virtual-demo:latest > trivy.txt" 
            }
        }
        stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker'){   
                         sh "docker push amuldark/virtual-demo:latest "
                    }
                }
            }
        }
        
        
        stage("Deploy"){
            steps{
                sh "docker-compose up -d" 
            }
        }
    }
}
