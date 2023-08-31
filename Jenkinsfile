pipeline{
    agent any
    tools {
     maven 'maven'   
    }
    stages {
        stage('Git Checkout') {
            steps {
                git url:'https://github.com/thamilgowtham/spring-boot-mongo.git', branch: 'master'
            }
        }
         stage('Maven Build'){
           steps{
              sh 'mvn clean package'
            }
        }
        stage('SonarQube Analysis') {
            steps{
              script{
                 withSonarQubeEnv(installationName: 'sonarqube',credentialsId: 'sonar-token') {
                 sh 'mvn sonar:sonar'
                 }
              }
           }
       }
       stage('Docker build & Docker image push to Nexus'){
         steps{
           script{
            withCredentials([string(credentialsId: 'nexus-credentials', variable: 'nexus_cred')]){
             sh '''
             docker build -t 35.92.208.60:8083/spring-boot-mongo .
             docker login -u admin -p ${nexus_cred} 35.92.208.60:8083
             docker push 35.92.208.60:8083/spring-boot-mongo
             docker rmi 35.92.208.60:8083/spring-boot-mongo
             '''
                 }
               }
            }
        }
        stage('Deploy Application in K8s Cluster'){
            steps {
                sh 'kubectl apply -f springboot.yml'
            }
        }
    }
}
