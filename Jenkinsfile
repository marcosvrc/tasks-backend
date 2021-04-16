pipeline {
    agent any
    stages {
        stage ('Build Backend'){
            steps {
                sh 'mvn clean package -DskipTests=true'
            }
        }
        stage ('Unit Tests'){
            steps {
                sh 'mvn test'
            }
        }
        //stage ('Sonar Analysis'){ VERIFICAR PORQUE TÁ DANDO TIMEOUT
            //environment {
             //   scannerHome = tool 'SONAR_SCANNER'
            //}
            //steps {
              //  withSonarQubeEnv('SONAR_LOCAL') {
                //    sh "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=824d03082ea24cf442ed66d2f46b1e8a004e6abb -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Aplication.java"
                //}
           //}
        //}
        //stage ('Quality Gate'){ VERIFICAR PORQUE ESTÁ DANDO ERRO
           // steps {
              //  sleep(5)
             //   timeout(time: 1, unit: 'MINUTES'){
              //      waitForQualityGate abortPipeline: true
                //}
            //}
        //}
        stage ('Deploy Backend') {
            steps{
                deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage ('API Test') {
            steps{
                dir('api-test'){
                    git 'https://github.com/marcosvrc/tasks-api-test'
                sh 'mvn test'
                }
            }
        }
        stage ('Deploy Frontend') {
            steps{
                dir('frontend'){
                    git 'https://github.com/marcosvrc/tasks-frontend'
                    sh 'mvn clean package'
                    deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }    
            }
        }
    }
}