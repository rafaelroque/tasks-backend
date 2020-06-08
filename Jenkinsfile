pipeline{
    agent any
    stages{
        stage('Build Backend'){
            steps{
              sh 'mvn clean package -DskipTests=true'
            }
            
        }
        stage('Unit Test'){
            steps{
              sh 'mvn test'
            }    
        }
        stage('Sonar Analysis'){
            environment{
                scannerHome = tool 'SONAR_SCANNER'
            }
            steps{
                withSonarQubeEnv('SONAR_LOCAL'){
                  sh "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBackend -Dsonar.host.url=http://localhost:9000 -Dsonar.login=da6086811056894b3e9512dc6e865ca6253dd8db -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/src/test**,**/model** "
                }
             }    
        }
        stage('Quality Gate'){
         steps{
           sleep(60)
           timeout(time:30 , unit:'MINUTES'){
             waitForQualityGate abortPipeline:true
           }
         }
        }
        stage('Deploy Backend'){
          steps{
              deploy adapters: [tomcat9(credentialsId: 'logintomcat', path: '', url: 'http://localhost:8080')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
          }
        }
    
    stage('API Test'){
          steps{
              dir('api-test'){
                git credentialsId: 'logintomcat', url: 'https://github.com/rafaelroque/tasks-api-test.git'
                sh 'mvn test'
              }
              
          }
        }
        stage('Deploy Frontend'){
          steps{
            dir('frontend'){
                git credentialsId: 'logintomcat', url: 'https://github.com/rafaelroque/tasks-frontend.git'
                sh 'mvn clean package'
                deploy adapters: [tomcat9(credentialsId: 'logintomcat', path: '', url: 'http://localhost:8080')], contextPath: 'tasks', war: 'target/tasks.war'
              }
            }
        }

        stage('Functional Test'){
          steps{
              dir('functional-test'){
                git credentialsId: 'logintomcat', url: 'https://github.com/rafaelroque/tasks-functional-test.git'
                sh 'mvn test'
              }
              
          }
        }
        stage('Deploy Prod'){
           steps{
             sh 'docker-compose build'
             sh 'docker-compose up -d'
           }
        }
    
    
    
    }
    
}
    


