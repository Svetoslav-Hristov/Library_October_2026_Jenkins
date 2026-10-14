pipeline{
    agent any
    
    
    stages{
        stage("Install Dependencies"){
            steps{
                bat 'npm install'
                echo "Installing Playwright"
                bat 'npx playwright install chromium'
            }
        }
        stage("Run Frontend and Backend "){
            steps{
                echo "Rinning Frontend"
                bat 'start "Frontend" /B cmd /C "npm run start"'
                echo "Rinning Backend"
                bat 'start "Backend" /B cmd /C "npm run server"'
                echo "Waiting for the server to start"
                bat 'npx wait-on http://localhost:3000'
            }
        }
        stage("Run Tests"){
            steps{
                echo "Running tests"
                bat 'npm run test'
            }
        }
    }
    
}