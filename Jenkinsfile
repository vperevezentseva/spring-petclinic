pipeline {

    agent any

    tools {
        maven 'Maven 4.0.0'
        jdk 'jdk8'
    }

    stages {

        stage ("Clone repo") {
            steps {
                git 'https://github.com/vperevezentseva/spring-petclinic.git'
            }
        }

        stage("Build") {
            steps {
                echo 'Build...'
            }
        }
        stage("Tests") {
            steps {
                echo 'Tests...'
            }
        }
        stage("Create docker image") {
            steps {
                echo 'Create...'
            }
        }
        stage("Upload docker image to Dcoker registry") {
            steps {
                echo 'Upload...'
            }
        }

    }

}
