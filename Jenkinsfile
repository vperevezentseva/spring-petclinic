pipeline {

    agent any
    tools {
        maven 'Maven 3.6.3'
/*        jdk 'jdk8'*/
    }

    environment {
        PROJECT = "vperevezentseva/spring-petclinic"
        registryCredential = 'dockerhubcreds'
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
        stage('Building Image') {
            steps{
                script {
                    dockerImage = docker.build ${env.PROJECT} + ":latest"
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
