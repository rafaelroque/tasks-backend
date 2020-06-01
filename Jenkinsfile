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
           sleep(5)
           timeout(time:1 , unit:'MINUTES'){
             waitForQualityGate abortPipeline:true
           }
         }
        }
        stage('Deploy Backend'){
          steps{
              deploy adapters: [tomcat9(credentialsId: 'logintomcat', path: '', url: 'http://localhost:8080')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
          }
        }
    }
    stage('API Test'){
          steps{
              git credentialsId: 'logintomcat', url: 'https://github.com/rafaelroque/tasks-api-test.git'
              echo 'mvn test'
          }
        }
    }
    
}

