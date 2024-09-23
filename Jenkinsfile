node {
    docker.image('node:16-buster-slim').inside('-p 3000:3000') {
        stage('Build') {
            sh 'npm install'
        }

        stage('Test') {
            sh './jenkins/scripts/test.sh'
        }

        stage('Manual Approval') {
            input message: 'Lanjutkan ke tahap Deploy?'
        }

        stage('Deploy') {
            sh 'apk add --no-cache openssh-client'
            sshagent(credentials: ['ec2-ssh-key']) {
                sh """
                    ssh -o StrictHostKeyChecking=no ec2-user@ec2-54-253-207-230.ap-southeast-2.compute.amazonaws.com "
                    sudo yum install -y nginx
                    sudo systemctl start nginx
                    sudo systemctl enable nginx
                    sudo mkdir -p /var/www/html
                    sudo chown ec2-user:ec2-user /var/www/html
                    "
                    scp target/my-app-1.0-SNAPSHOT.jar ec2-user@ec2-54-253-207-230.ap-southeast-2.compute.amazonaws.com:/var/www/html/
                """
            }
            sleep(time: 1, unit: 'MINUTES')
            sh './jenkins/scripts/deliver.sh'
        }
    }
}