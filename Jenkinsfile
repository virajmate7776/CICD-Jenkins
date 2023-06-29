pipeline {
    agent any
    environment {
      PATH = "$PATH:/opt/apache-maven-3.9.3/bin"
    }
    
    stages {

        stage('CLEAN WORKSPACE') {
            steps {
                cleanWs()
            }
        }

        stage('CODE CHECKOUT') {
            steps {
                git 'https://github.com/virajmate7776/CICD-Jenkins.git'
            }
        }

    }

}  
