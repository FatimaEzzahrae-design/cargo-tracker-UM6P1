pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        SONAR_TOKEN = credentials('sonarqube-token')
    }

    stages {

        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/FatimaEzzahrae-design/cargo-tracker-UM6P1.git'
            }
        }

        stage('Start Payara Micro') {
    steps {
        bat 'java -jar payara-micro.jar --deploy target/cargo-tracker.war --port 8080'
    }
}


        stage('Build & Test with Coverage') {
            steps {
                bat 'mvnw.cmd clean verify'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeLocal') {
                    bat """
                        mvnw.cmd sonar:sonar ^
                        -Dsonar.projectKey=Tp-Jenkins ^
                        -Dsonar.projectName=Tp-Jenkins ^
                        -Dsonar.branch.name=main ^
                        -Dsonar.login=%SONAR_TOKEN%
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        success {
            echo 'Build et analyse terminés avec succès !'
        }
        failure {
            echo 'Échec du build, des tests ou de la Quality Gate.'
        }
    }
}
