pipeline {
    agent any
    stages {
        stage ('Build Backend'){
            steps {
                sh 'mvn clean package -DskipTests=true'
            }
        }
    }
    stages {
        stage ('Unit Tests'){
            steps {
                sh 'mvn test'
            }
        }
    }
}