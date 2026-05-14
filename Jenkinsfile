pipeline {
    agent any

    environment {
        VM_IP       = '20.233.250.200'
        TARGET_PATH = '/var/www/html'
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Deploy to Azure VM') {
            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'uat-admin-vm-safegold',
                        usernameVariable: 'VM_USERNAME',
                        passwordVariable: 'VM_PASSWORD'
                    )
                ]) {

                    sh '''
                        echo "Checking sshpass..."

                        if ! command -v sshpass > /dev/null; then
                            sudo apt-get update
                            sudo apt-get install -y sshpass
                        fi

                        echo "Deploying PHP application to Azure VM..."

                        sshpass -p "$VM_PASSWORD" rsync -avz --delete \
                            -e "ssh -o StrictHostKeyChecking=no" \
                            ./ $VM_USERNAME@$VM_IP:$TARGET_PATH/

                        echo "Deployment completed successfully."
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful.'
        }

        failure {
            echo 'Deployment failed.'
        }
    }
}
