pipeline {

    agent any

    tools {
        maven 'Maven 3.6.3'
    }

    environment {
        IMAGE = "vperevezentseva/spring-petclinic"
    }

    stages {

        stage("Build Maven") {
            steps {
                sh 'mvn -B -DskipTests clean package' 
                echo '||| Builded...'
            }
        }
        stage("Tests Maven") {
            steps {
                sh 'mvn test'
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
                  if ( env.BRANCH_NAME == 'master' ) {
                    pom = readMavenPom file: 'pom.xml'
                    TAG = pom.version
                  } else {
                    TAG = env.BRANCH_NAME
                  }
                  sh "docker build -t ${env.IMAGE}:${TAG} ."
                }
                echo '||| Created docker image...'
            }
        }

        stage('Push to Dockerhub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhubcreds', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUsername')]) {
                    script {
                        sh "docker login -u ${env.dockerUsername} -p ${env.dockerPassword}"
                        sh "docker push ${env.IMAGE}:${TAG}"
                    echo '||| Uploaded docker image to docker registry...'
                    }
                }
            }
        }


        stage('Run local container') {
            steps {
                echo '||| Run docker container...'
                sh 'docker rm -f petclinic-temp || true'
                sh "docker run -d --name petclinic-temp ${env.IMAGE}:${TAG}"
            }
        }
        stage('Stop local container') {
            steps {
                sh 'docker kill petclinic-temp || true'
                sh 'docker rm -f petclinic-temp || true'
            }
        }
    }
}
