pipeline {
    agent any

    triggers {
        githubPush()   
    }

    stages {

        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/FatimaEzzahrae-design/cargo-tracker-UM6P1.git'
            }
        }

        stage('Start Payara') {
            steps {
                bat 'start java -jar target\\cargo-tracker.war --port 8080'
                bat 'ping 127.0.0.1 -n 15 > nul'
            }
        }

        stage('Build & Test with Coverage') {
            steps {
                bat 'mvnw clean verify'
            }
        }
    }

    post {
        success {
            echo 'Build et analyse terminés avec succès !'
        }
        failure {
            echo 'Échec du build ou des tests.'
        }
    }
}
