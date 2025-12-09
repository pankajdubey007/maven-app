pipeline {
    agent any
 
    tools {
        jdk 'jdk21'
    }
 
    environment {
        PATH = "${env.JAVA_HOME}/bin:/opt/maven/bin:${env.PATH}"
        DOCKER_IMAGE = 'simple-java-maven-app:latest'
    }
 
    stages {
 
        stage('Debug Java') {
            steps {
                sh 'java -version'
                sh 'echo JAVA_HOME=$JAVA_HOME'
            }
        }
 
        stage('Checkout Code') {
            steps {
                git 'https://github.com/pankajdubey007/maven-app.git, branch: 'main'
            }
        }
 
        stage('Build Maven Project') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
 
        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t $DOCKER_IMAGE .
                '''
            }
        }
 
        stage('Cleanup Docker') {
            steps {
                sh '''
                    docker stop simple-java-app || true
                    docker rm simple-java-app || true
 
                    dangling=$(docker images -f "dangling=true" -q)
                    if [ -n "$dangling" ]; then
                        docker rmi -f $dangling || true
                    fi
                '''
            }
        }
 
        stage('Run Docker Container') {
            steps {
                sh '''
                    docker run -d --name simple-java-app -p 8081:8080 $DOCKER_IMAGE
                '''
            }
        }
    }
 
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
