
pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    environment {
        PROJECT_DIR = "${WORKSPACE}"
        ANSIBLE = "/home/ansible/.local/bin/ansible"
        PLAYBOOK = "/home/ansible/.local/bin/ansible-playbook"
        INVENTORY = "inventory"
        SSH_KEY = "/var/lib/jenkins/.ssh/id_rsa"
        REMOTE_USER = "ansible"
    }

    stages {
        stage('1. Git Checkout') {
            steps {
                echo 'Downloading project from GitHub'
                checkout scm
            }
        }

        stage('2. Validate Files') {
            steps {
                sh '''
                    set -eu

                    test -s Jenkinsfile
                    test -s inventory
                    test -s deploy.yml
                    test -s index.html

                    echo "All required project files exist."
                '''
            }
        }

        stage('3. Validate Inventory') {
            steps {
                sh '''
                    set -eu

                    "$ANSIBLE" --version

                    /home/ansible/.local/bin/ansible-inventory \
                      -i "$INVENTORY" --list > inventory-check.json

                    grep -q '"dev"' inventory-check.json
                    grep -q '172.31.9.152' inventory-check.json

                    echo "Inventory validation passed."
                '''
            }
        }

        stage('4. Ansible Syntax Check') {
            steps {
                sh '''
                    set -eu

                    "$PLAYBOOK" \
                      -i "$INVENTORY" \
                      deploy.yml \
                      --syntax-check

                    echo "Playbook syntax is valid."
                '''
            }
        }

        stage('5. SSH and Ansible Connectivity') {
            steps {
                sh '''
                    set -eu

                    test -r "$SSH_KEY"

                    "$ANSIBLE" \
                      -i "$INVENTORY" \
                      dev \
                      -m ping \
                      -u "$REMOTE_USER" \
                      --private-key="$SSH_KEY"
                '''
            }
        }

        stage('6. Deploy Website') {
            steps {
                sh '''
                    set -eu

                    "$PLAYBOOK" \
                      -i "$INVENTORY" \
                      deploy.yml \
                      -u "$REMOTE_USER" \
                      --private-key="$SSH_KEY"
                '''
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Website deployed to AWS EC2.'
        }

        failure {
            echo 'FAILED: Review the stage and error in Console Output.'
        }

        always {
            echo 'Jenkins pipeline finished.'
        }
    }
}
