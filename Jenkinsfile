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

        stage('Download Payara Micro') {
    steps {
        bat 'curl -L -o payara-micro.jar https://repo1.maven.org/maven2/fish/payara/micro/6.2023.4/payara-micro-6.2023.4.jar'
    }
}


        stage('Start Payara Micro') {
    steps {
        bat 'start /B java -jar payara-micro.jar --deploy target/cargo-tracker.war --port 8080'
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
                -Dsonar.login=%SONAR_TOKEN%
            """
        }
    }
}


        stage('Quality Gate') {
            steps {
                timeout(time: 60, unit: 'MINUTES') {
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
