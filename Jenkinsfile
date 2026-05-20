pipeline {
    agent any

     stages {

        stage('maven build') {
            steps {
                sh 'sudo mvn clean install'
            }
        }

        stage('docker build') {
            steps {
                sh 'sudo docker build -t sara:latest -f /home/ec2-user/Dockerfile.'
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh '''
                        echo "$DOCKER_PASS" | sudo docker login -u "$DOCKER_USER" --password-stdin

                        sudo docker tag sara:latest sara567/sara:latest

                        sudo docker push sara567/sara:latest
                    '''
                }
            }
        }

        stage('Run Ansible Playbook via SSH') {
            steps {
                sshagent(['sarasherin']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ansible@172.31.4.175 "ansible-playbook /home/ansible/playbook/deplo.yml"
                    '''
                }
            }
        }

    }
}
