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
                withSonarQubeEnv('sonar') {
                sh 'mvn clean package sonar:sonar'
                }
            }   
        }

        stage("Sonar Qality Gates"){
            when{ expression { params.action == 'create'}}
            steps{
                waitForQualityGate abortPipeline: false, credentialsId: 'sonar'
            }
        }

        stage("MVN Build"){
            when{ expression { params.action == 'create'}}
            steps{
                sh 'mvn clean install'
            }
        }

        stage("Docker Image Build"){
            when{ expression { params.action == 'create'}}
            steps{
                sh '''
                    docker image build -t yaswanth345/ecart .
                    docker image tag yaswanth345/ecart yaswanth345/ecart:v1
                '''
            }
        }

        stage("Docker Image Scan : Trivy"){
            when{ expression { params.action == 'create'}}
            steps{
                sh '''
                    trivy image yaswanth345/ecart:v1 > scan.txt
                    cat scan.txt
                '''
            }
        }

         stage("Docker Image Push"){
            when{ expression { params.action == 'create'}}
           steps{
                withCredentials([usernamePassword(credentialsId: 'docker', passwordVariable: 'pass', usernameVariable: 'username')]) {
                    sh "docker login -u '$username' -p '$pass'"
                }
                sh "docker image push yaswanth345/ecart:v1"
            }
        }

        stage("Deploy as Container"){
            when{ expression { params.action == 'create'}}
           steps{
                // withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'pass', usernameVariable: 'username')]) {
                //     sh "docker login -u '$username' -p '$pass'"
                // }   
                sh "docker run -d --name ecart -p 8070:8070 yaswanth345/ecart:v1"
            }
        }

        stage("Docker Container clean up"){
            when{ expression { params.action == 'destroy'}}
           steps{  
                sh "docker stop yaswanth345/ecart:v1"
                sh "docker rmi -f yaswanth345/ecart:v1"
            }
        }
    }
}
