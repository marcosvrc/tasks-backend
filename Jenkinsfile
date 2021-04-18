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
        stage ('Functional Test') {
            steps{
                dir('functional-test'){
                    git 'https://github.com/marcosvrc/tasks-functional_tests'
                //sh 'mvn test'
                }
            }
        }
        stage ('Deploy PRD') {
            steps{
                sh 'docker-compose build'
                sh 'docker-compose up -d'
            }
        }
        stage ('Heatlth Check Fronted') {
            steps{
                sleep(30)
               dir('functional-test'){
                   sh 'mvn verify -Dskip.surefire.tests'
               }
            }
        }
    }
    post{
        always{
            junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml, api-test/target/surefire-reports/*.xml, functional-test/target/surefire-reports/*.xml, functional-test/target/failsafe-reports/*.xml'
            chuckNorris()
            archiveArtifacts artifacts: 'targer/tasks-backend.war, frontend/target/tasks.war', onlyIfSuccessful: true
        }
        /*unsuccessful { Envio de email quando ocorrer erro 
            emailext attachLog: true, body: 'See the attached log below', subject: 'Build $BUILD_NUMBER has failed', to: 'deploy+jenkinks@gmail.com' 
        }
        fixed { Envio de email quando depois de um erro der tudo certo
            emailext attachLog: true, body: 'See the attached log below', subject: 'Build is fine!!!', to: 'deploy+jenkinks@gmail.com' 
        }*/
    }
}