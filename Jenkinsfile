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
        stage('Checkout SCM') {
            steps {
                // When using a 'docker' agent, Jenkins automatically checks out the SCM
                // into the container's workspace. No explicit 'git' step needed here unless
                // you have multiple repositories or complex checkout requirements.
                checkout scm
            }
        }
        stage('Build') {
            steps {
                echo "Building for Production.."
                sh '''
                    cd myapp
                    # Create a Python virtual environment (this will now work correctly)
                    python3 -m venv venv
                    # Activate the virtual environment using the POSIX-compliant '.' command
                    . venv/bin/activate
                    # Install dependencies into the isolated virtual environment
                    pip install -r requirements.txt
                '''
            }
        }
        stage('Test') {
            steps {
                echo "Testing for Production.."
                sh '''
                    cd myapp
                    # Activate the virtual environment for testing
                    . venv/bin/activate
                    # Run your Python tests
                    python hello.py
                    python hello.py --name=phonerapterx
                '''
            }
        }
        stage('Deliver') {
            steps {
                echo 'Delivering for Production....'
                sh '''
                    cd myapp
                    # Activate the virtual environment if delivery scripts depend on installed packages
                    . venv/bin/activate
                    echo "Performing production delivery steps..."
                    # Example: Copy artifacts, deploy to a server, build a Docker image for deployment
                    # cp -r . /path/to/deployment/target
                    # python deploy_script.py
                '''
            }
        }
    }
}