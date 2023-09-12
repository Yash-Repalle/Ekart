//@Library(my-shared-lib)

pipeline {

    agent any

    parameters{

        choice(name:'action', choices: 'create\ndestroy', description: 'select create or destroy')
    }
    
    stages {
        stage('Git Checkout') {
            when{ expression { params.action == 'create'}}
            steps {
                git branch: 'main', url: 'https://github.com/Yash-Repalle/Ekart.git'
            }
        }
        
        stage('COMPILE') {
            when{ expression { params.action == 'create'}}
            steps {
                sh "mvn clean compile -DskipTests=true"
            }
        }

        stage('TEST'){
            when{ expression { params.action == 'create'}}
            steps{
                sh 'mvn test'
            }
        }
        
        stage('INTIGRATION TEST'){
            when{ expression { params.action == 'create'}}
            steps{
                sh 'mvn verify -DskipUnitTests'
            }
        }

        stage('Sonar Qube Code Analysis'){
            when{ expression { params.action == 'create'}}
            steps{
                withSonarQubeEnv('sonarqube') {
                sh 'mvn clean package sonar:sonar'
                }
            }   
        }

        stage("Sonar Qality Gates"){

            steps{
                waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube'
            }
        }

        stage("MVN Build"){

            steps{
                sh 'mvn clean install'
            }
        }

        stage("Docker Image Build"){
            steps{


                sh '''
                    docker image build -t yaswanth345/ecart .
                    docker image tag yaswanth345/ecart:v1
                '''
            }
        }
    }
}
