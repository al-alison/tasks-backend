pipeline {
    agent any
    stages{
        stage('Build Backend'){
            steps{
                bat 'mvn clean package -DskipTests=true'
            }
        }
         stage('Unit Tests'){
            steps{
                bat 'mvn test'
            }
        }
        stage('Deploy Backend'){
            steps{
               deploy adapters: [tomcat8(credentialsId: 'tomcat_user', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage('API Test'){
            steps{
                dir('api-test'){
                    git 'https://github.com/al-alison/tasks-api-test.git'
                    bat 'mvn test'
                } 
            }
        }
         stage('Deploy Frontend'){
            steps{
                dir('frontend'){
                    git 'https://github.com/al-alison/tasks-frontend.git'
                    bat 'mvn clean package'
                    deploy adapters: [tomcat8(credentialsId: 'tomcat_user', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }
            }
        }
        stage('Deploy Prod'){
            steps{
                bat 'docker-compose build'
                bat 'docker-compose up -d'
            }
        }
    }
    post{
        always{
            junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml, api-test/target/surefire-reports/*.xml'
        }
        unsuccessful{
            emailext attachLog: true, body: 'See the attached log below', subject: 'Build $BUILD_NUMBER has failed', to: 'alison.almeida00@gmail.com'
        }
        fixed{
            emailext attachLog: true, body: 'See the attached log below', subject: 'Build $BUILD_NUMBER is fine', to: 'alison.almeida00@gmail.com'
        }
    }
}
