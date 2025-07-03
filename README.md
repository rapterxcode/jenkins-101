# Jenkins 101

A comprehensive Jenkins CI/CD learning project demonstrating containerized Jenkins setup with Docker agents and automated Python application pipelines.

## 🚀 Quick Start

### Prerequisites

- Docker installed and running
- Docker Compose (optional, for easier management)
- Git
- Basic knowledge of Jenkins and CI/CD concepts

### Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd jenkins-101
   ```

2. **Build Jenkins Master**
   ```bash
   docker build -t myjenkins-blueocean:2.504.3-1 ./Jenkins_Install
   ```

3. **Build Jenkins Agent**
   ```bash
   docker build -t phonepasit9/myjenkinsagents:python ./Jenkins_agent
   ```

4. **Run Jenkins Master**
   ```bash
   docker run --name jenkins-blueocean --restart=on-failure --detach \
      --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
      --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
      --publish 8080:8080 --publish 50000:50000 \
      --volume jenkins-data:/var/jenkins_home \
      --volume jenkins-docker-certs:/certs/client:ro \
      myjenkins-blueocean:2.504.3-1
   ```

5. **Access Jenkins**
   - Open http://localhost:8080 in your browser
   - Get initial admin password: `docker exec jenkins-master cat /var/jenkins_home/secrets/initialAdminPassword`
   - Complete the setup wizard

## 📋 Configuration

### Setting Up Jenkins Agent

1. **In Jenkins Dashboard:**
   - Go to "Manage Jenkins" → "Manage Nodes and Clouds"
   - Click "New Node"
   - Name: `docker-agent-python`
   - Select "Permanent Agent"
   ```bash
   docker run -d --restart=always -p 127.0.0.1:2376:2375 --network jenkins -v /var/run/docker.sock:/var/run/docker.sock alpine/socat tcp-listen:2375,fork,reuseaddr unix-connect:/var/run/docker.sock
   
   docker inspect <container_id> | grep IPAddress
   ```
2. **Configure Agent:**
   - Remote root directory: `/home/jenkins`
   - Labels: `docker-agent-python`
   - Launch method: "Launch agent by connecting it to the master"

3. **Run Agent Container:**
   ```bash
   docker run -d \
     --name docker-agent-python \
     -v jenkins_agent_workspace:/home/jenkins \
     phonepasit9/myjenkinsagents:python \
     java -jar agent.jar -jnlpUrl http://HOST_IP:8080/computer/docker-agent-python/slave-agent.jnlp -secret SECRET_KEY
   ```

### Creating Pipeline Job

1. **New Item** → **Pipeline**
2. **Pipeline Configuration:**
   - Definition: "Pipeline script from SCM"
   - SCM: Git
   - Repository URL: Your repository URL
   - Branch: `*/main`
   - Script Path: `Jenkinsfile`

## 🔧 Usage

### Running the Python Application Locally

```bash
# Navigate to the application directory
cd myapp

# Create virtual environment
python3 -m venv venv

# Activate virtual environment
source venv/bin/activate  # On Linux/Mac
# OR
venv\Scripts\activate     # On Windows

# Install dependencies
pip install -r requirements.txt

# Run the application
python hello.py                    # Output: Hello World!
python hello.py --name=YourName   # Output: Hello YourName!
```

### Pipeline Execution

The Jenkins pipeline automatically:

1. **Build Stage:**
   - Creates Python virtual environment
   - Installs dependencies from `requirements.txt`

2. **Test Stage:**
   - Runs application with default parameters
   - Tests with custom name parameter

3. **Deliver Stage:**
   - Prepares for production deployment
   - Customizable deployment steps

### Monitoring Pipeline

- **Blue Ocean UI:** http://localhost:8080/blue
- **Classic UI:** http://localhost:8080/job/YOUR_JOB_NAME
- **SCM Polling:** Automatically triggers every 5 minutes

## 📁 Project Structure

```
jenkins-101/
├── Jenkins_Install/
│   └── Dockerfile              # Jenkins master configuration
├── Jenkins_agent/
│   └── Dockerfile              # Python agent configuration
├── myapp/
│   ├── hello.py               # Main Python application
│   └── requirements.txt       # Python dependencies
├── Jenkinsfile                # Production pipeline
├── Jenkinsfile.template       # Template pipeline
├── helloworld.py             # Basic Python script
└── README.md                 # This file
```

## 🛠️ Troubleshooting

### Common Issues

1. **Agent Connection Issues**
   - Ensure Jenkins master is accessible from agent container
   - Check firewall settings for port 50000
   - Verify correct secret key and JNLP URL

2. **Pipeline Failures**
   - Check agent label matches (`docker-agent-python`)
   - Verify Python and pip are installed in agent
   - Ensure virtual environment creation succeeds

3. **Permission Issues**
   - Verify Docker socket permissions
   - Check Jenkins home directory permissions
   - Ensure agent workspace is writable

### Useful Commands

```bash
# Check Jenkins logs
docker logs jenkins-master

# Check agent logs
docker logs jenkins-python-agent

# Access Jenkins container
docker exec -it jenkins-master bash

# Clean up containers
docker stop jenkins-master jenkins-python-agent
docker rm jenkins-master jenkins-python-agent
```

## 🔍 Learning Objectives

This project demonstrates:

- **Containerized Jenkins Setup**: Master and agent architecture
- **Pipeline as Code**: Jenkinsfile with multiple stages
- **Python Virtual Environments**: Isolated dependency management
- **SCM Integration**: Automated polling and builds
- **Docker Integration**: Container-based CI/CD workflow
- **Plugin Management**: BlueOcean and Docker workflow plugins

## 🎯 Next Steps

1. **Extend the Pipeline:**
   - Add code quality checks (linting, static analysis)
   - Implement automated testing with pytest
   - Add deployment to staging/production environments

2. **Enhance Security:**
   - Configure RBAC (Role-Based Access Control)
   - Set up credential management
   - Enable HTTPS with SSL certificates

3. **Scale the Setup:**
   - Add more agent nodes
   - Implement distributed builds
   - Set up build artifact management

## 📚 Additional Resources

- [Jenkins Documentation](https://www.jenkins.io/doc/)
- [Docker Official Documentation](https://docs.docker.com/)
- [Jenkins Pipeline Syntax](https://www.jenkins.io/doc/book/pipeline/syntax/)
- [Blue Ocean Documentation](https://www.jenkins.io/doc/book/blueocean/)
