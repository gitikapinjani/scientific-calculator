pipeline {
    agent any

    triggers{
        githubPush()
    }
    tools {
        maven 'maven3'
    }

    environment {
        DOCKER_CREDENTIALS_ID = 'DockerHubCred'
        IMAGE_NAME = 'gitikapinjani19/scientific-calculator'
        IMAGE_TAG = 'latest'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/gitikapinjani/scientific-calculator.git'
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn clean test'
                sh 'mvn package'
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        def app = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                        app.push()
                    }
                }
            }
        }

        stage('Deploy using Ansible') {
            steps {
                script {
                    withEnv(["ANSIBLE_HOST_KEY_CHECKING=False"]) {
                        ansiblePlaybook(
                            playbook: 'deploy.yml',
                            inventory: 'inventory'
                        )
                    }
                }
            }
        } 
    } 
}
