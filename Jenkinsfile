pipeline {

    agent any

    environment {
        IMAGE = "vperevezentseva/spring-petclinic"
    }

    stages {

        stage("Build") {
            steps {
                sh 'mvn -B -DskipTests clean package' 
                echo '||| Builded...'
            }
        }
        stage("Tests") {
            steps {
                sh 'mvn test'
                echo '||| Tested...'
            }
            post {
                always {
                    junit 'reports/*.xml'
                }
            }
        }
        stage("Build docker image") {
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

        stage('Run local') {
            steps {
                sh 'docker rm -f petclinic-temp || true'
                sh "docker run -d --name petclinic-temp ${env.IMAGE}:${TAG}"
            }
        }
        stage('Stop local container') {
            steps {
                sh 'docker rm -f petclinic-temp || true'
            }
        }

        stage('Push to Dockerhub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhubcreds', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUsername')]) {
                    echo '||| Uploaded docker image to docker registry...'
                    script {
                        sh "docker login -u ${env.dockerUsername} -p ${env.dockerPassword}"
                        sh "docker push ${env.IMAGE}:${TAG}"
                    }
                }
            }
        }
    }
}
