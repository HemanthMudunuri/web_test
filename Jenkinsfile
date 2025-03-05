pipeline {
    agent any
    
    environment {
        EC2_IP = '65.0.122.131'  // Replace with your EC2 Public IP
        SSH_KEY = 'C:\\Users\\1000684\\Downloads\\testing_key.pem' // Full Windows path
        GIT_REPO = 'https://github.com/adityathakureka/web_test.git'
        REPO_DIR = 'C:\\Jenkins\\workspace\\web_test'
    }
    
    stages {
        stage('Clone or Pull Latest Code') {
            steps {
                script {
                    echo 'Fetching the latest code from GitHub...'
                    bat """
                    IF EXIST "%REPO_DIR%" (
                        cd /d "%REPO_DIR%" && git reset --hard && git pull origin main
                    ) ELSE (
                        "C:\\Users\\1000684\\AppData\\Local\\Programs\\Git\\cmd\\git.exe" clone -b main "%GIT_REPO%" "%REPO_DIR%"
                    )
                    """
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    echo 'Installing dependencies...'
                    bat """
                    cd /d "%REPO_DIR%"
                    call npm ci
                    """
                }
            }
        }

        stage('Build React App') {
            steps {
                script {
                    echo 'Building React app...'
                    bat """
                    cd /d "%REPO_DIR%"
                    call npm run build
                    """
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                script {
                    echo 'Deploying to EC2...'
                    bat """
                    cmd /c ssh -i "%SSH_KEY%" ec2-user@%EC2_IP% "sudo rm -rf /usr/share/nginx/html/*"
                    cmd /c scp -i "%SSH_KEY%" -r "%REPO_DIR%\\build\\*" ec2-user@%EC2_IP%:/usr/share/nginx/html
                    cmd /c ssh -i "%SSH_KEY%" ec2-user@%EC2_IP% "sudo systemctl restart nginx"
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful!'
        }
        failure {
            echo '❌ Deployment failed! Check logs for errors.'
        }
    }
}
