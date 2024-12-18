pipeline { 
    options { 
        timestamps() 
    } 
    environment {
        DOCKER_CREDS = credentials('tockenocker')
    }
    agent none 
    stages {  
        stage('Check scm') {  
            agent any 
            steps { 
                checkout scm 
            } 
        } // stage Check scm

        stage('Build') {  
            steps { 
                echo "Building ...${BUILD_NUMBER}" 
                echo "Build completed" 
            } 
        } // stage Build

        stage('Test') { 
            agent { 
                docker { 
                    image 'python:3.9-alpine' 
                    args '-u root' 
                } 
            } 
            steps { 
                sh 'apk add --update python3 py3-pip' 
                sh 'pip install xmlrunner' 
                sh 'python3 Test.py' 
            } 
            post { 
                always { 
                    junit 'test-reports/*.xml' 
                } 
                success { 
                    echo "Application testing successfully completed" 
                } 
                failure { 
                    echo "Oooppss!!! Tests failed!" 
                }  
            } // post 
        } // stage Test
    
        stage("Publish") {
            agent any
            steps {
                sh 'echo $DOCKER_CREDS_PSW | docker login --username $DOCKER_CREDS_USR --password-stdin'
                sh 'docker build -t DmytroMDobrovolsky/notes:latest .'
                sh 'docker push DmytroMDobrovolsky/notes:latest'
            } 
        } // stage Publish
    } // stages
} // pipeline
