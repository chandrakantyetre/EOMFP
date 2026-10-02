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

        stage('Docker Push') {
    steps {
        withCredentials([usernamePassword(
            credentialsId: 'dockerhub-eomfp',
            usernameVariable: 'DOCKERHUB_USERNAME',
            passwordVariable: 'DOCKERHUB_TOKEN'
        )]) {

            powershell '''
                $docker = "C:\\Users\\HP\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe"

                & $docker tag `
                    "eomfp-product-service:build-$env:BUILD_NUMBER" `
                    "$env:DOCKERHUB_USERNAME/eomfp:build-$env:BUILD_NUMBER"

                $env:DOCKERHUB_TOKEN | & $docker login `
                    -u $env:DOCKERHUB_USERNAME `
                    --password-stdin

                if ($LASTEXITCODE -ne 0) {
                    exit $LASTEXITCODE
                }

                & $docker push `
                    "$env:DOCKERHUB_USERNAME/eomfp:build-$env:BUILD_NUMBER"

                if ($LASTEXITCODE -ne 0) {
                    exit $LASTEXITCODE
                }
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