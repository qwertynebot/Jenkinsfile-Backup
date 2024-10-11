pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET')
        S3_BUCKET = 'qubixitstep'  // Назва вашого S3 відра
        BACKEND_POD = 'backend-deployment-6ff9fd966b-rxkgw' // Назва вашого бекенд пода
        FRONTEND_POD = 'frontend-deployment-797dcf5c9d-szt5m' // Назва вашого фронтенд пода
        DATABASE_NAME = 'facebook' // Ім'я вашої бази даних
        DB_USER = 'postgres' // Користувач бази даних
        DB_PASSWORD = 'Z6!mNp9wA&v3Qd#Xr7Tf$2gL' // Пароль користувача бази даних
        RDS_ENDPOINT = 'database-qubix.cvcwm8usccw2.eu-north-1.rds.amazonaws.com' // Ваш RDS endpoint
    }

    stages {
        stage('Backup Frontend Files') {
            steps {
                script {
                    echo "Backing up frontend files from Kubernetes"
                    sh """
                        # Копіювання файлів з контейнера фронтенда
                        kubectl cp $FRONTEND_POD:/usr/share/nginx/html ./frontend_files_backup --container frontend-container-name
                        # Архівування
                        tar -czf frontend_files_backup.tar.gz -C ./frontend_files_backup .
                    """
                }
            }
        }

        stage('Backup Backend Files') {
            steps {
                script {
                    echo "Backing up backend files from Kubernetes"
                    sh """
                        # Копіювання скомпільованих файлів з контейнера бекенда
                        kubectl cp $BACKEND_POD:/app/publish ./backend_files_backup --container backend-container-name
                        # Копіювання статичних файлів
                        kubectl cp $BACKEND_POD:/app/images ./backend_files_backup/images --container backend-container-name
                        kubectl cp $BACKEND_POD:/app/EmailTemplates ./backend_files_backup/EmailTemplates --container backend-container-name
                        # Архівування
                        tar -czf backend_files_backup.tar.gz -C ./backend_files_backup .
                    """
                }
            }
        }

        stage('Backup Database') {
            steps {
                script {
                    echo "Backing up database"
                    sh """
                        # Експорт бази даних з Amazon RDS
                        mysqldump -h $RDS_ENDPOINT -u $DB_USER -p$DB_PASSWORD $DATABASE_NAME > database_backup.sql
                    """
                }
            }
        }

        stage('Upload Backups to S3') {
            steps {
                script {
                    echo "Uploading backups to S3"
                    sh """
                        # Завантаження архівів до S3
                        aws s3 cp frontend_files_backup.tar.gz s3://$S3_BUCKET/
                        aws s3 cp backend_files_backup.tar.gz s3://$S3_BUCKET/
                        aws s3 cp database_backup.sql s3://$S3_BUCKET/
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
