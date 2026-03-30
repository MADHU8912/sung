pipeline {
    agent any

    triggers {
        cron('H/5 * * * *')
    }

    environment {
        PROJECT_NAME = 'sung'
        RELEASE_DIR  = 'release'
        DEPLOY_DIR   = 'C:\\deploy\\sung'
        ZIP_NAME     = 'sung-release.zip'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'CI/CD Pipeline Started'
                checkout scm
            }
        }

        stage('Show Files') {
            steps {
                bat 'dir'
            }
        }

        stage('Prepare Release Folder') {
            steps {
                bat """
                if exist %RELEASE_DIR% rmdir /s /q %RELEASE_DIR%
                mkdir %RELEASE_DIR%
                """
            }
        }

        stage('Build') {
            steps {
                echo 'Building project...'
                bat """
                echo Sung build successful > %RELEASE_DIR%\\build-report.txt
                copy index.html %RELEASE_DIR%\\index.html
                """
            }
        }

        stage('Test') {
            steps {
                echo 'Running simple test...'
                bat """
                if exist %RELEASE_DIR%\\index.html (
                    echo Test Passed > %RELEASE_DIR%\\test-report.txt
                    type %RELEASE_DIR%\\test-report.txt
                ) else (
                    echo Test Failed: index.html not found
                    exit /b 1
                )
                """
            }
        }

        stage('Package') {
            steps {
                echo 'Creating release zip...'
                bat """
                if exist %ZIP_NAME% del /f /q %ZIP_NAME%
                powershell -Command "Compress-Archive -Path %RELEASE_DIR%\\* -DestinationPath %ZIP_NAME% -Force"
                """
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying project...'
                bat """
                if not exist "%DEPLOY_DIR%" mkdir "%DEPLOY_DIR%"
                copy /Y %RELEASE_DIR%\\index.html "%DEPLOY_DIR%\\index.html"
                copy /Y %RELEASE_DIR%\\build-report.txt "%DEPLOY_DIR%\\build-report.txt"
                copy /Y %RELEASE_DIR%\\test-report.txt "%DEPLOY_DIR%\\test-report.txt"
                copy /Y %ZIP_NAME% "%DEPLOY_DIR%\\%ZIP_NAME%"
                """
            }
        }
    }

    post {
        success {
            archiveArtifacts artifacts: 'sung-release.zip, release/*', fingerprint: true
            echo 'Build, test, package, and deployment completed successfully'
        }

        failure {
            echo 'Pipeline failed'
        }

        always {
            echo 'Pipeline finished'
        }
    }
}