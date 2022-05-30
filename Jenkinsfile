pipeline {

    agent any
    tools {
        maven 'Maven 3.6.3'
        jdk 'jdk8'
    }

    environment {
        PROJECT = "vperevezentseva/spring-petclinic"
        registryCredential = 'dockerhubcreds'
        dockerImage = ''
    }


    stages {

        stage('Cloning Git') {
            steps {
                git "https://github.com/${env.PROJECT}.git"
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
/*        stage("Create docker image") {
            steps {
                script {
                  if ( env.BRANCH_NAME == 'main' ) {
                    pom = readMavenPom file: 'pom.xml'
                    TAG = pom.version
                  } else {
                    TAG = env.BRANCH_NAME
                  }
                  sh "docker build -t ${env.PROJECT}:${TAG} ."
                }
                echo '||| Created docker image...'
            }
        }*/

/*        stage('Push to Dockerhub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhubcreds', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUsername')]) {
                    script {
                        sh "docker login -u ${env.dockerUsername} -p ${env.dockerPassword}"
                        sh "docker push ${env.PROJECT}:${TAG}"
                    echo '||| Uploaded docker image to docker registry...'
                    }
                }
            }
        }*/

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
