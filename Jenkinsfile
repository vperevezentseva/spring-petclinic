pipeline {

    agent any
    tools {
        maven 'Maven 3.6.3'
    }

    environment {
        PROJECT = "spring-petclinic"
        dockerCredential = 'dockerhubcreds'
        dockerImage = ''
    }


    stages {

        stage('Cloning Git') {
            steps {
                git "https://github.com/vperevezentseva/spring-petclinic.git"
            }
        }
        stage('Compile') {
           steps {
             sh 'mvn compile'
           }
        }
        stage("Build Maven") {
            steps {
                sh '''
                mvn -B -DskipTests clean package
                pwd
                ls -lah
                '''
                echo '||| Builded...'
            }
        }
        stage("Tests Maven") {
            steps {
                sh '''
                mvn test

                '''
                echo '||| Tested...'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }


        stage("Create docker image") {
            steps {
                script {
                  sh "docker build -t ${env.PROJECT}:latest ."
                }
                echo '||| Created docker image...'
            }
        }
        stage('Building Image') {
            steps{
                script {
                    dockerImage = docker.build ${env.PROJECT} + ":latest"
                    echo '||| Created docker image...'
                }
            }
        }
        stage('Deploy Image') {
            steps{
                script {
                    docker.withRegistry( '', dockerCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }



        stage('Run local container') {
            steps {
                echo '||| Run docker container...'
                sh "docker rm -f petclinic-temp || true"
                sh "docker run -d --name petclinic-temp ${env.PROJECT}:${TAG}"
            }
        }
        stage('Stop local container') {
            steps {
                sh "docker kill petclinic-temp || true"
                sh "docker rm -f petclinic-temp || true"
            }
        }
    }
}
