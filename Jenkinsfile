pipeline {
    agent any

    environment {
        // Використовуйте Jenkins Credentials для AWS ключів
        AWS_ACCESS_KEY_ID = credentials('aws-access-key-id') // Заміна на ім'я ваших креденшалів
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key') // Заміна на ім'я ваших креденшалів
        S3_BUCKET = 'qubixitstep'  // Вкажіть ваше S3 відро
        BACKEND_SERVICE_IP = 'a06ef3430318f44b580f93b8fff8db6a-86025589.eu-north-1.elb.amazonaws.com'
        FRONTEND_SERVICE_IP = 'a4fb534dc54a44d0a9e92d6fa94ac944-920711934.eu-north-1.elb.amazonaws.com'
    }

    stages {
        stage('Backup Backend Service') {
            steps {
                script {
                    echo "Backing up Backend Service"
                    // Зберігаємо бекенд конфігурацію або дані
                    sh """
                        echo "Fetching data from backend service"
                        curl -s -o backend_backup.txt http://$BACKEND_SERVICE_IP:5181 || { echo 'Backend service request failed'; exit 1; }
                        
                        echo "Uploading to S3"
                        aws s3 cp backend_backup.txt s3://$S3_BUCKET/backend_backup.txt || { echo 'S3 upload failed'; exit 1; }
                    """
                }
            }
        }

        stage('Backup Frontend Service') {
            steps {
                script {
                    echo "Backing up Frontend Service"
                    // Зберігаємо фронтенд конфігурацію або дані
                    sh """
                        echo "Fetching data from frontend service"
                        curl -s -o frontend_backup.txt http://$FRONTEND_SERVICE_IP:80 || { echo 'Frontend service request failed'; exit 1; }
                        
                        echo "Uploading to S3"
                        aws s3 cp frontend_backup.txt s3://$S3_BUCKET/frontend_backup.txt || { echo 'S3 upload failed'; exit 1; }
                    """
                }
            }
        }
    }

    post {
        always {
            echo "Backup completed"
        }
    }
}
