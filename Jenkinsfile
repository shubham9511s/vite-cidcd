pipeline {
    agent any
    environment{
        SONAR_HOME=tool "sonar"
    }


    stages {
        stage('Git SCM') {
            steps {
                echo 'Git checkout process is running'
                git branch: 'main', url: 'https://github.com/shubham9511s/vite-cidcd.git'
            }
        }
        
         stage('Sonar-scanner quality check') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh"cd vite-project"
                    sh"$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=vito-app -Dsonar.projectKey=vito-app"    
              }
            }
        }
        stage('OWASP dependency Check') {
            steps {
                    dependencyCheck additionalArguments: '--scan ./   ', odcInstallation: 'DC'
                   dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                
            }
        }
        stage('docker bulid -tag & push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                          sh "docker build -t vitoapp vite-project/."
                          sh "docker tag vitoapp shubhamshinde2025/vitoapp:v1"
                          sh "docker push shubhamshinde2025/vitoapp:v1"  
                }    
                }
            }
        }
     
        stage('trivy-scan docker image') {
            steps {
                echo"Trivy Scan Images"
                sh"trivy image --format table -o trivy_report.html shubhamshinde2025/vitoapp:v1"
                
            }
        }
        
        stage('deploy on container') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        echo"docker image deploy on container "
                        sh "docker run -d -p 5173:5173 shubhamshinde2025/vitoapp:v1 "
                         
                }
                    
                }   
            }
        }
        
        
    }
}
