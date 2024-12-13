pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        maven 'maven3'
        
    }
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('git checkout') {
            steps {
                git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/shrikantdandge665/FullStack-Blogging-App.git'
            }
        }
        
         stage('Compile') {
            steps {
                sh " mvn compile"
            }
        }
        
         stage('Test') {
            steps {
                sh " mvn test"
            }
        }
        
         stage('Trivy Fs scan') {
            steps {
                sh "trivy fs --format table -o fs.html ."
            }
        }
        
         stage('Sonarqube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                 sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Blogging-app -Dsonar.projectName=Blogging-app \
                 -Dsonar.java.binaries=target '''
              }
            }
        }
        
         stage('Build') {
            steps {
                sh "mvn package"
            }
        }
        
         stage('Deploy to nexus') {
            steps {
                withMaven(globalMavenSettingsConfig: 'maven-settings', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                    sh "mvn deploy"   
                }
            }
        }
        
        stage('Build &tag') {
            steps {
                script {
                withDockerRegistry(credentialsId: 'docker-cred') {
                    sh "docker build -t shrikantdandge7/blogging-app:latest ."
                  }
                }
            }
        }
    
        
        stage('trivy scan') {
            steps {
                sh "trivy image --format table -o image.html shrikantdandge7/blogging-app:latest"
            }
        }
        
         stage('Docker image push') {
            steps {
                script {
                withDockerRegistry(credentialsId: 'docker-cred') {
                    sh "docker push shrikantdandge7/blogging-app:latest"
                  }
                }
            }
        }
    }  
}

