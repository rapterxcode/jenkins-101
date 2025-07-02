pipeline {
    agent {
        node {
            label 'docker-agent-python'
        }
    }
    triggers {
        pollSCM '* * * * *'
    }
    stages {
        /* stage('Checkout SCM') {
            steps {
                // Explicitly define the Git repository and the branch to clone
                // *** IMPORTANT: Replace 'main' with your actual branch name if it's different ***
                git branch: 'main', url: 'https://github.com/devopsjourney1/jenkins-101.git'
            }
        } */
        stage('Build') {
            steps {
                echo "Building.."
                sh '''
                    cd myapp
                    pip install -r requirements.txt
                '''
            }
        }
        stage('Test') {
            steps {
                echo "Testing.."
                sh '''
                    cd myapp
                    # Run your Python scripts directly
                    python3 hello.py
                    python3 hello.py --name=phonerapterx
                '''
            }
        }
        stage('Deliver') {
            steps {
                echo 'Deliver....'
                sh '''
                    echo "doing delivery stuff.."
                '''
            }
        }
    }
}