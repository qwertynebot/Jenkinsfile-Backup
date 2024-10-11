pipeline {
    agent any

    environment {
        // Використовуйте Jenkins Credentials для AWS ключів
        AWS_ACCESS_KEY_ID = credentials('AKIAUJ3VUBFTK7MJ7T7P')
        AWS_SECRET_ACCESS_KEY = credentials('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')
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
                        # Вибірка даних з backend-сервісу
                        curl http://$BACKEND_SERVICE_IP:5181 > backend_backup.txt
                        
                        # Завантаження в S3
                        aws s3 cp backend_backup.txt s3://$S3_BUCKET/backend_backup.txt
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
                        # Вибірка даних з frontend-сервісу
                        curl http://$FRONTEND_SERVICE_IP:80 > frontend_backup.txt
                        
                        # Завантаження в S3
                        aws s3 cp frontend_backup.txt s3://$S3_BUCKET/frontend_backup.txt
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
