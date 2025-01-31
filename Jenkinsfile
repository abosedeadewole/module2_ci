pipeline{
  agent any 
  tools { maven 'maven'}
  stages{
    stage('git-clone'){
      steps{
         checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'git-check', url: 'https://github.com/MyBoseOrg/module2_ci']]])
      }
    }
    stage('etech-hello'){
      steps{
  sh 'git version'
  sh 'mvn -v'
      }
    }
    stage('Build Artifact - Maven') {
       steps {
         sh "mvn clean package -DskipTests=true"
         archive 'target/*.jar'
       }
    }
    stage('Unit Tests - JUnit and JaCoCo') {
       steps {
         sh 'mvn test' 
       }
       post {
         always {
           junit 'target/surefire-reports/*.xml'
           jacoco execPattern: 'target/jacoco.exec'
         }
       }
     }      
    stage('Mutation Tests - PIT') {
      steps {
        sh "mvn org.pitest:pitest-maven:mutationCoverage"
      }
      post {
        always {
          pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
        }
      }
    }    
    stage('CodeQuality - SAST') {
      steps {
        sh 'mvn clean verify sonar:sonar \
  -Dsonar.projectKey=devsecops-spring-app \
  -Dsonar.host.url=http://etechdevopdemo.eastus.cloudapp.azure.com:9000 \
  -Dsonar.login=54aee578297d7a1177c4b487f9158c29216a7005'
            }
          }
        }
      }
