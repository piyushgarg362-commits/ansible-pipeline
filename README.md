
# Jenkins + Ansible AWS EC2 Deployment

## Project Overview

This project automates website deployment to an AWS EC2 instance
using Jenkins Pipeline and Ansible.

## Architecture

GitHub -> Jenkins -> Ansible -> AWS EC2 -> Nginx Website

## Technologies Used

- AWS EC2
- Linux / Amazon Linux
- Jenkins
- Ansible
- SSH key authentication
- Nginx
- Git and GitHub
- YAML and Bash

## Pipeline Stages

1. Checkout project from GitHub
2. Validate required project files
3. Validate Ansible inventory
4. Check playbook syntax
5. Test SSH connectivity
6. Deploy website
7. Verify Nginx responds with HTTP 200

## Project Files

- Jenkinsfile: Jenkins pipeline
- inventory: Ansible target configuration
- deploy.yml: Website deployment playbook
- index.html: Website content
- README.md: Project documentation
- .gitignore: Files excluded from Git

## Setup Requirements

- A running Jenkins controller
- Ansible installed on the Jenkins execution node
- An Amazon Linux EC2 target
- SSH access from Jenkins to the target
- Nginx available through the target's package repositories
- Permission for the remote user to run required sudo tasks
- Inbound TCP port 80 allowed in the target's security group

## Deployment

Configure the Jenkins job as Pipeline script from SCM
and point it to this GitHub repository.

The Jenkins execution user must be able to read the configured
SSH private key. Never commit private keys or passwords to GitHub.

After a successful build, open the target EC2 public IP using HTTP.
