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
    }
}
