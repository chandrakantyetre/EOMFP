pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube-EOMFP') {
                    bat 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:5.8.0.7211:sonar -Dsonar.projectKey=eomfp-product-service -Dsonar.token=%SONAR_AUTH_TOKEN%'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Docker Build') {
            steps {
                bat '"C:\\Users\\HP\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe" build -t eomfp-product-service:build-%BUILD_NUMBER% .'
            }
        }

        stage('Docker Credential Test') {
    steps {
        withCredentials([string(
            credentialsId: 'dockerhub-eomfp-token',
            variable: 'DOCKERHUB_TOKEN'
        )]) {

            powershell '''
                $bytes = [System.Text.Encoding]::UTF8.GetBytes($env:DOCKERHUB_TOKEN)
                $hash = [System.Security.Cryptography.SHA256]::Create().ComputeHash($bytes)
                ($hash | ForEach-Object { $_.ToString("x2") }) -join ""
            '''
        }
    }
}
        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }
}