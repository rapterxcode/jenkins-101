# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Jenkins CI/CD learning project that demonstrates containerized Jenkins setup with Docker agents. The project includes:
- Jenkins master server setup with Docker support
- Python-based Jenkins agent configuration
- Sample Python application with automated pipeline
- Complete build, test, and delivery pipeline automation

## Architecture

### Jenkins Infrastructure
- **Jenkins Master**: Containerized Jenkins server (Jenkins_Install/Dockerfile) with Docker CLI, BlueOcean, and workflow plugins
- **Jenkins Agent**: Python-enabled Docker agent (Jenkins_agent/Dockerfile) with Python 3, pip, and venv support
- **Agent Label**: `docker-agent-python` - used for pipeline execution

### Pipeline Architecture
- **Main Pipeline**: `Jenkinsfile` - Production pipeline with SCM polling, virtual environment management, and comprehensive build/test/delivery stages
- **Template Pipeline**: `Jenkinsfile.template` - Simplified template for basic pipeline structure
- **Trigger**: SCM polling every 5 minutes (`H/5 * * * *`)

### Application Structure
- **myapp/**: Main Python application directory
  - `hello.py`: CLI application using Fire framework for argument parsing
  - `requirements.txt`: Python dependencies (fire==0.4.0)
- **helloworld.py**: Basic Python hello world script (separate from main app)

## Common Commands

### Building Jenkins Infrastructure
```bash
# Build Jenkins master
docker build -t jenkins-master ./Jenkins_Install

# Build Jenkins agent
docker build -t jenkins-python-agent ./Jenkins_agent

# Run Jenkins master
docker run -d -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home jenkins-master
```

### Application Development
```bash
# Set up Python environment (matches pipeline)
cd myapp
python3 -m venv venv
. venv/bin/activate
pip install -r requirements.txt

# Run application
python hello.py
python hello.py --name=YourName

# Test basic script
python ../helloworld.py
```

### Pipeline Testing
The Jenkins pipeline automatically:
1. Creates isolated Python virtual environment
2. Installs dependencies from requirements.txt
3. Runs application tests with default and custom parameters
4. Performs delivery steps

## Key Implementation Details

### Virtual Environment Management
The pipeline uses POSIX-compliant virtual environment activation (`. venv/bin/activate`) to ensure compatibility across different shell environments.

### Docker Agent Configuration
- Agent runs with Python 3, pip, and venv pre-installed
- Uses `docker-agent-python` label for pipeline targeting
- Automatically handles SCM checkout in containerized environment

### Pipeline Stages
- **Build**: Virtual environment setup and dependency installation
- **Test**: Application execution with parameter testing
- **Deliver**: Production deployment preparation (customizable)

## Development Notes

- The main application uses the Fire library for CLI argument parsing
- Pipeline includes comprehensive virtual environment isolation
- SCM polling is configured for continuous integration
- Template pipeline available for creating new similar projects