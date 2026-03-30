pipeline {
    agent any

    triggers {
        cron('H/5 * * * *')
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code'
            }
        }

        stage('Show Files') {
            steps {
                bat 'dir'
            }
        }

        stage('Build') {
            steps {
                bat 'if not exist release mkdir release'
                bat 'echo Sung build successful > release\\build-report.txt'
                bat 'copy index.html release\\index.html'
            }
        }

        stage('Release') {
            steps {
                bat 'powershell -Command "Compress-Archive -Path release\\* -DestinationPath sung-release.zip -Force"'
            }
        }
    }

    post {
        success {
            archiveArtifacts artifacts: 'sung-release.zip, release/build-report.txt, release/index.html', fingerprint: true
            echo 'Build and release completed successfully'
        }
        failure {
            echo 'Build failed'
        }
    }
}