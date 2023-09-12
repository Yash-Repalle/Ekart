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
        
    }
}
