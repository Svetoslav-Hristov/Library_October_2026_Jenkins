pipeline {
    agent any

    stages {
        stage("Install Dependencies") {
            steps {
                bat 'npm install'
                echo "Installing Playwright"
                bat 'npx playwright install chromium'
            }
        }

        stage("Run Frontend and Backend") {
            steps {
                echo "Running Frontend"
                bat 'start "Frontend" /B cmd /C "npm run start"'

                echo "Running Backend"
                bat 'start "Backend" /B cmd /C "npm run server"'

                echo "Waiting for the server to start"
                bat 'npx wait-on http://localhost:3000'
            }
        }

        stage("Run Tests") {
            steps {
                echo "Running tests"
                bat 'npm run test'
            }
        }

        stage('Deploy to Render') {
            steps {
                echo 'Deploying backend to Render'

                withCredentials([
                    string(
                        credentialsId: 'RENDER_BACKEND_HOOK',
                        variable: 'RENDER_BACKEND_HOOK'
                    )
                ]) {
                    bat 'curl.exe --fail --silent --show-error -X POST "%RENDER_BACKEND_HOOK%"'
                }

                echo 'Deploying frontend to Render'

                withCredentials([
                    string(
                        credentialsId: 'RENDER_FRONTEND_HOOK',
                        variable: 'RENDER_FRONTEND_HOOK'
                    )
                ]) {
                    bat 'curl.exe --fail --silent --show-error -X POST "%RENDER_FRONTEND_HOOK%"'
                }
            }
        }
        
        stage('Verify Render Deployment') {
            steps {
                script {
                    timeout(time: 10, unit: 'MINUTES') {
                        waitUntil {
                            sleep(time: 10, unit: 'SECONDS')

                            echo 'Checking frontend...'
                            def frontendStatus = bat(
                                returnStatus: true,
                                script: 'curl.exe --fail --silent --show-error --max-time 10 "https://library-jenkins-fe.onrender.com"'
                            )

                            echo 'Checking backend...'
                            def backendStatus = bat(
                                returnStatus: true,
                                script: 'curl.exe --fail --silent --show-error --max-time 10 "https://library-jenkins-be.onrender.com"'
                            )

                            if (frontendStatus == 0 && backendStatus == 0) {
                                echo 'SUCCESS: Frontend and backend are responding!'
                                return true
                            }

                            echo 'Services are not ready yet. Retrying in 10 seconds...'
                            return false
                        }
                    }
                }
            }
        }

    }
}
