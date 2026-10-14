pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    environment {
        ANSIBLE_PLAYBOOK = '/home/ansible/.local/bin/ansible-playbook'
        ANSIBLE_HOST_KEY_CHECKING = 'False'
    }

    stages {
        stage('Validate Files') {
            steps {
                sh '''
                    set -eu
                    test -s Jenkinsfile
                    test -s inventory
                    test -s deploy.yml
                    test -s index.html
                    echo "All project files found."
                '''
            }
        }

        stage('Ansible Syntax Check') {
            steps {
                sh '''
                    set -eu
                    "$ANSIBLE_PLAYBOOK" \
                        -i inventory \
                        deploy.yml \
                        --syntax-check
                '''
            }
        }

        stage('Deploy to EC2') {
            steps {
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'ec2-ssh-key',
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'SSH_USER'
                    )
                ]) {
                    sh '''
                        set -eu
                        test -r "$SSH_KEY"

                        "$ANSIBLE_PLAYBOOK" \
                            -i inventory \
                            deploy.yml \
                            --private-key "$SSH_KEY" \
                            -u "$SSH_USER"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Website deployed using Jenkins and Ansible!'
        }
        failure {
            echo 'FAILED: Check Jenkins Console Output.'
        }
        always {
            echo 'Pipeline execution completed.'
        }
    }
}
