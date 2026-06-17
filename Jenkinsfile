pipeline {
    agent any

    environment {
        SERVER_IP      = '172.31.89.103'
        SSH_CREDENTIAL = 'node-app-ssh-key'
        REPO_SHOPEASE  = 'https://github.com/surajjagtap221/ShopEase-Website.git'
        REPO_FOODHUB   = 'https://github.com/surajjagtap221/FoodHub-Restaurant-Website.git'
        BRANCH         = 'main'
        REMOTE_USER    = 'ubuntu'
        REMOTE_PATH    = '/home/ubuntu/code/'
        
    }
    
    
    stages {
        stage('Clone Repository') {
            steps {
                git branch: "${BRANCH}", url: "${REPO_SHOPEASE}"
                git branch: "${BRANCH}", url: "${REPO_FOODHUB}"
            }
        }

        stage('Upload Files to target-server') {
            steps {
                sshagent([SSH_CREDENTIAL]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${SERVER_IP} 'mkdir -p ${REMOTE_PATH}'
                        scp -o StrictHostKeyChecking=no -r * ${REMOTE_USER}@${SERVER_IP}:${REMOTE_PATH}/
                    """
                }
            }
        }

        stage('Install Dependencies & Start App on the target server') {
            steps {
                sshagent([SSH_CREDENTIAL]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${SERVER_IP} '
                            cd ${REMOTE_PATH} &&
                            sudo apt update && sudo apt install -y nginx &&
                            sudo systemctl enable --now nginx &&
                            sudo cp -r ${REMOTE_PATH}/* /var/www/html/ &&
                            cd /var/www/html/
                            sudo mv FoodHub-Restaurant-Website foodhub &&
                            sudo mv ShopEase-Website shopease &&
                            sudo systemctl restart nginx 
                        '
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Application deployed successfully!'
        }
        failure {
            echo '❌ Deployment failed.'
        }
    }
}



