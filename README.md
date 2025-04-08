# Docker Cheatsheet

A comprehensive reference guide for Docker commands, concepts, and best practices.

## Table of Contents
- [Understanding Containerization](#understanding-containerization)
- [Basic Concepts](#basic-concepts)
- [Installation](#installation)
- [Windows Subsystem for Linux (WSL2)](#windows-subsystem-for-linux-wsl2)
- [Docker Commands](#docker-commands)
  - [Images](#images)
  - [Containers](#containers)
  - [Volumes](#volumes)
  - [Networks](#networks)
  - [Docker Compose](#docker-compose)
- [Dockerfile Reference](#dockerfile-reference)
- [Practical Exercises](#practical-exercises)
- [Best Practices](#best-practices)
- [Troubleshooting](#troubleshooting)

## Understanding Containerization

Containerization is a lightweight alternative to full machine virtualization that involves encapsulating an application in a container with its own operating environment.

### The Core Idea

Imagine you're a developer who has created an application. This application works perfectly on your computer, but when you share it with others, they encounter problems. Why? Because their environments are different from yours - they might have different operating system versions, different library versions, or different configurations.

This is where containerization comes in. A container packages your application along with all its dependencies, libraries, and configuration files, ensuring it runs the same way regardless of the environment.

### How Containerization Differs from Virtual Machines

**Virtual Machines (VMs):**
- Each VM includes a full copy of an operating system, applications, and necessary binaries/libraries
- Typically consumes GBs of memory and takes minutes to boot
- Provides complete isolation but at the cost of efficiency

**Containers:**
- Share the host system's kernel but have their own filesystem, processes, and network
- Typically consume MBs of memory and start in seconds
- Provide isolation at the application level with minimal overhead

![Docker vs VM Architecture](https://www.docker.com/sites/default/files/d8/2018-11/docker-containerized-appliction-blue-border_2.png)

### Real-World Examples

1. **Development Environment Consistency**
   ```
   Problem: "It works on my machine" syndrome
   Solution: Containerize the development environment so all developers work with identical setups
   Example: A team uses a Docker container with Node.js, MongoDB, and all required dependencies
   ```

2. **Microservices Architecture**
   ```
   Problem: Complex applications are difficult to maintain as monoliths
   Solution: Break the application into containerized microservices
   Example: An e-commerce site with separate containers for user authentication, product catalog, 
            shopping cart, and payment processing
   ```

3. **Continuous Integration/Continuous Deployment (CI/CD)**
   ```
   Problem: Testing and deployment environments differ
   Solution: Use the same container throughout the pipeline
   Example: Code is built and tested in a container, then that exact same container is deployed to production
   ```

4. **Application Isolation**
   ```
   Problem: Applications with conflicting dependencies
   Solution: Each application runs in its own container with its specific dependencies
   Example: One application requires Python 2.7 while another needs Python 3.8 - both run on the same host
   ```

5. **Scalability**
   ```
   Problem: Handling variable loads efficiently
   Solution: Spin up additional containers during high demand, remove them when demand decreases
   Example: A news website that automatically scales up during breaking news events
   ```

### Benefits of Containerization

- **Consistency**: Applications run the same way in development, testing, and production
- **Isolation**: Applications run independently without interfering with each other
- **Portability**: Containers can run on any system that supports the container runtime
- **Efficiency**: Containers share the host OS kernel and use fewer resources than VMs
- **Speed**: Containers start in seconds, enabling rapid scaling and deployment
- **Version Control**: Container images can be versioned, allowing easy rollbacks

## Basic Concepts

### Images
- **Docker Image**: A read-only template containing instructions for creating a Docker container
- **Base Image**: A parent image that provides the foundation for other images
- **Official Image**: Curated and maintained by Docker
- **Docker Hub**: A registry service for Docker images
- **Image Layers**: Each instruction in a Dockerfile creates a layer, which is cached to speed up builds

### Containers
- **Docker Container**: A runnable instance of a Docker image
- **Container Lifecycle**: Created, Running, Paused, Stopped, Deleted
- **Ephemeral**: Containers are designed to be temporary and disposable
- **Stateless vs Stateful**: Containers should ideally be stateless, but can be stateful with volumes

### Volumes
- **Docker Volume**: Persistent data storage that exists outside of containers
- **Bind Mount**: Maps a host file or directory to a container file or directory
- **tmpfs Mount**: Stores data in memory only
- **Volume Drivers**: Allow volumes to be stored on remote hosts or cloud providers

### Networks
- **Bridge Network**: Default network driver for containers on the same host
- **Host Network**: Removes network isolation between container and host
- **Overlay Network**: Connects multiple Docker daemons and enables swarm services
- **Macvlan Network**: Assigns a MAC address to a container, making it appear as a physical device
- **Network Namespaces**: Provide isolation for container networking

### Docker Architecture
- **Docker Daemon (dockerd)**: Background service that manages Docker objects
- **Docker Client**: Command-line interface to interact with Docker
- **Docker Registry**: Stores Docker images (Docker Hub is a public registry)
- **Docker Objects**: Images, containers, networks, volumes, plugins, etc.

## Installation

### Windows
```bash
# Download Docker Desktop from https://www.docker.com/products/docker-desktop
# Follow the installation wizard
# Ensure WSL2 is enabled for better performance
```

### macOS
```bash
# Download Docker Desktop from https://www.docker.com/products/docker-desktop
# Follow the installation wizard
```

### Linux (Ubuntu)
```bash
# Update package index
sudo apt-get update

# Install prerequisites
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common

# Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# Set up the stable repository
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

# Update package index again
sudo apt-get update

# Install Docker CE
sudo apt-get install docker-ce

# Verify installation
sudo docker run hello-world

# (Optional) Add your user to the docker group to run Docker without sudo
sudo usermod -aG docker $USER
# Log out and log back in for this to take effect
```

## Windows Subsystem for Linux (WSL2)

WSL2 is a key component for running Docker on Windows systems. It provides a Linux kernel compatibility layer that allows Linux binaries to run natively on Windows.

### What is WSL2?
- **Architecture**: WSL2 uses a real Linux kernel and lightweight VM, providing full Linux compatibility
- **Performance**: Offers significantly better performance than WSL1, especially for filesystem operations
- **Docker Integration**: Docker Desktop for Windows uses WSL2 as its backend, allowing containers to run in a Linux environment
- **System Requirements**:
  - Windows 10 version 1903 or higher (x64 systems)
  - Windows 10 version 2004 or higher (ARM64 systems)
  - Virtualization enabled in BIOS/UEFI

### Common WSL2 Commands
```bash
# Check WSL status
wsl --status

# Install WSL2
wsl --install

# List installed Linux distributions
wsl --list --verbose

# Set WSL2 as default
wsl --set-default-version 2

# Restart WSL
wsl --shutdown
```

### Troubleshooting WSL2
- If Docker Desktop can't connect to the Docker daemon, ensure WSL2 is properly installed and running
- For filesystem performance issues, store your project files in the Linux filesystem rather than Windows-mounted drives
- For memory issues, you can limit WSL2 resource usage by creating a `.wslconfig` file in your Windows user directory

## Docker Commands

### Images

```bash
# List all images
docker images
docker image ls

# Pull an image from Docker Hub
docker pull [image_name]:[tag]

# Build an image from a Dockerfile
docker build -t [name]:[tag] [path_to_dockerfile_directory]

# Remove an image
docker rmi [image_id/name]

# Remove all unused images
docker image prune

# Show image history
docker history [image_name]

# Save an image to a tar archive
docker save -o [output_file.tar] [image_name]

# Load an image from a tar archive
docker load -i [input_file.tar]

# Tag an image
docker tag [source_image]:[tag] [target_image]:[tag]

# Search for images on Docker Hub
docker search [term]

# Inspect an image
docker image inspect [image_id/name]
```

### Containers

```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# Create and start a container
docker run [options] [image_name]:[tag]

# Common run options:
#   -d, --detach          Run in background
#   -p, --publish         Publish container ports to host
#   -v, --volume          Bind mount a volume
#   --name                Assign a name to the container
#   --rm                  Remove container when it exits
#   -e, --env             Set environment variables
#   -it                   Interactive mode with terminal
#   --network             Connect to a network

# Start a stopped container
docker start [container_id/name]

# Stop a running container
docker stop [container_id/name]

# Restart a container
docker restart [container_id/name]

# Remove a container
docker rm [container_id/name]

# Remove all stopped containers
docker container prune

# Execute a command in a running container
docker exec [options] [container_id/name] [command]

# View container logs
docker logs [options] [container_id/name]

# Inspect container details
docker inspect [container_id/name]

# Show container resource usage statistics
docker stats [container_id/name]

# Copy files between container and host
docker cp [container_id]:[container_path] [host_path]
docker cp [host_path] [container_id]:[container_path]

# Create a new image from a container
docker commit [container_id/name] [new_image_name]:[tag]

# Pause all processes in a container
docker pause [container_id/name]

# Unpause a container
docker unpause [container_id/name]

# Show port mappings for a container
docker port [container_id/name]
```

### Volumes

```bash
# List all volumes
docker volume ls

# Create a volume
docker volume create [volume_name]

# Inspect a volume
docker volume inspect [volume_name]

# Remove a volume
docker volume rm [volume_name]

# Remove all unused volumes
docker volume prune

# Run a container with a volume
docker run -v [volume_name]:[container_path] [image_name]

# Run a container with a bind mount
docker run -v [host_path]:[container_path] [image_name]

# Run a container with a tmpfs mount
docker run --tmpfs [container_path] [image_name]

# Create a volume with a specific driver
docker volume create --driver [driver_name] [volume_name]

# Back up a volume
docker run --rm -v [volume_name]:/source -v $(pwd):/backup alpine tar -czf /backup/backup.tar.gz -C /source .
```

### Networks

```bash
# List all networks
docker network ls

# Create a network
docker network create [options] [network_name]

# Inspect a network
docker network inspect [network_name]

# Connect a container to a network
docker network connect [network_name] [container_id/name]

# Disconnect a container from a network
docker network disconnect [network_name] [container_id/name]

# Remove a network
docker network rm [network_name]

# Remove all unused networks
docker network prune

# Create a network with a specific subnet
docker network create --subnet=192.168.0.0/16 [network_name]

# Create an overlay network for swarm services
docker network create --driver overlay [network_name]
```

### Docker Compose

```bash
# Start services defined in docker-compose.yml
docker-compose up [options]
#   -d, --detach          Run in background

# Stop services
docker-compose down [options]
#   -v                    Remove volumes
#   --rmi                 Remove images

# List running services
docker-compose ps

# View service logs
docker-compose logs [service_name]

# Execute a command in a service container
docker-compose exec [service_name] [command]

# Build or rebuild services
docker-compose build [service_name]

# Pull service images
docker-compose pull [service_name]

# Scale services
docker-compose up -d --scale [service_name]=[num_instances]

# Show service configuration
docker-compose config

# Validate and view the Compose file
docker-compose config

# Restart services
docker-compose restart [service_name]

# Pause services
docker-compose pause [service_name]

# Unpause services
docker-compose unpause [service_name]
```

## Dockerfile Reference

A Dockerfile is a text file that contains instructions for building a Docker image.

```dockerfile
# Base image
FROM [image]:[tag]

# Set working directory
WORKDIR [path]

# Copy files from host to container
COPY [source] [destination]

# Add files from URL or tar
ADD [source] [destination]

# Run commands
RUN [command]

# Set environment variables
ENV [key]=[value]

# Define volumes
VOLUME [path]

# Expose ports
EXPOSE [port]

# Define the command to run when container starts
CMD ["executable", "param1", "param2"]

# Define the entrypoint
ENTRYPOINT ["executable", "param1", "param2"]

# Set user
USER [username/uid]

# Set build arguments
ARG [name]=[default_value]

# Add metadata
LABEL [key]=[value]

# Set health check
HEALTHCHECK [options] CMD [command]

# Set shell
SHELL ["executable", "parameters"]
```

### Multi-stage builds

```dockerfile
# Build stage
FROM golang:1.16 AS builder
WORKDIR /app
COPY . .
RUN go build -o myapp

# Final stage
FROM alpine:latest
COPY --from=builder /app/myapp /usr/local/bin/
CMD ["myapp"]
```

## Practical Exercises

### Exercise 1: Building Your First Container

Follow this progressive exercise to understand the basics of Docker:

#### Step 1: Create a simple web application
```bash
# Create a project directory
mkdir docker-web-app && cd docker-web-app

# Create a simple index.html file
cat > index.html << EOF
<!DOCTYPE html>
<html>
<head>
    <title>Docker Test</title>
</head>
<body>
    <h1>Hello from Docker!</h1>
    <p>If you can see this, your Docker container is running correctly.</p>
</body>
</html>
EOF
```

#### Step 2: Create a Dockerfile
```bash
# Create a Dockerfile
cat > Dockerfile << EOF
# Use nginx as the base image
FROM nginx:alpine

# Copy our HTML file to the default nginx serving directory
COPY index.html /usr/share/nginx/html/

# Expose port 80
EXPOSE 80
EOF
```

#### Step 3: Build the Docker image
```bash
# Build the image and tag it
docker build -t my-web-app:1.0 .
```

#### Step 4: Run the container
```bash
# Run the container, mapping port 8080 on the host to port 80 in the container
docker run -d -p 8080:80 --name web-app my-web-app:1.0
```

#### Step 5: Test the application
```bash
# Open a browser and navigate to http://localhost:8080
# You should see your HTML page
```

#### Step 6: Modify and update
```bash
# Edit the index.html file to add some color
cat > index.html << EOF
<!DOCTYPE html>
<html>
<head>
    <title>Docker Test</title>
    <style>
        body { background-color: #f0f0f0; font-family: Arial, sans-serif; }
        h1 { color: #0066cc; }
        p { color: #333333; }
    </style>
</head>
<body>
    <h1>Hello from Docker!</h1>
    <p>If you can see this, your Docker container is running correctly.</p>
    <p>This page has been updated!</p>
</body>
</html>
EOF

# Rebuild the image with a new tag
docker build -t my-web-app:1.1 .

# Stop and remove the old container
docker stop web-app
docker rm web-app

# Run a new container with the updated image
docker run -d -p 8080:80 --name web-app my-web-app:1.1
```

### Exercise 2: Multi-Container Application with Docker Compose

This exercise demonstrates how to set up a multi-container application using Docker Compose:

#### Step 1: Create a project structure
```bash
# Create a project directory
mkdir docker-compose-demo && cd docker-compose-demo

# Create directories for each service
mkdir -p web-frontend api-backend database
```

#### Step 2: Set up the web frontend
```bash
# Create a simple HTML file
cat > web-frontend/index.html << EOF
<!DOCTYPE html>
<html>
<head>
    <title>Multi-Container App</title>
    <script>
        async function fetchData() {
            const response = await fetch('http://localhost:5000/api/data');
            const data = await response.json();
            document.getElementById('result').textContent = JSON.stringify(data, null, 2);
        }
    </script>
</head>
<body>
    <h1>Multi-Container Application Demo</h1>
    <button onclick="fetchData()">Fetch Data from API</button>
    <pre id="result"></pre>
</body>
</html>
EOF

# Create a Dockerfile for the frontend
cat > web-frontend/Dockerfile << EOF
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/
EXPOSE 80
EOF
```

#### Step 3: Set up the API backend
```bash
# Create a simple Flask API
cat > api-backend/app.py << EOF
from flask import Flask, jsonify
from flask_cors import CORS
import os
import pymongo

app = Flask(__name__)
CORS(app)

@app.route('/api/data')
def get_data():
    client = pymongo.MongoClient(os.environ.get('MONGO_URI', 'mongodb://database:27017/'))
    db = client.testdb
    
    # Insert some data if collection is empty
    if db.items.count_documents({}) == 0:
        db.items.insert_many([
            {'name': 'Item 1', 'value': 100},
            {'name': 'Item 2', 'value': 200},
            {'name': 'Item 3', 'value': 300}
        ])
    
    # Get all items
    items = list(db.items.find({}, {'_id': 0}))
    return jsonify(items)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
EOF

# Create requirements.txt
cat > api-backend/requirements.txt << EOF
flask==2.0.1
flask-cors==3.0.10
pymongo==3.12.0
EOF

# Create a Dockerfile for the backend
cat > api-backend/Dockerfile << EOF
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY app.py .
EXPOSE 5000
CMD ["python", "app.py"]
EOF
```

#### Step 4: Create a docker-compose.yml file
```bash
# Create the docker-compose.yml file
cat > docker-compose.yml << EOF
version: '3'

services:
  frontend:
    build: ./web-frontend
    ports:
      - "8080:80"
    depends_on:
      - backend

  backend:
    build: ./api-backend
    ports:
      - "5000:5000"
    environment:
      - MONGO_URI=mongodb://database:27017/
    depends_on:
      - database

  database:
    image: mongo:latest
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db

volumes:
  mongo-data:
EOF
```

#### Step 5: Run the multi-container application
```bash
# Start all services
docker-compose up -d

# Check the status of the services
docker-compose ps
```

#### Step 6: Test the application
```bash
# Open a browser and navigate to http://localhost:8080
# Click the "Fetch Data from API" button to see data from the MongoDB database
```

#### Step 7: Scale the application
```bash
# Scale the backend service to 3 instances
docker-compose up -d --scale backend=3
```

## Best Practices

### Security
- Run containers with non-root users
- Use official images from trusted sources
- Regularly update base images
- Scan images for vulnerabilities
- Use secrets management for sensitive data
- Limit container capabilities and resources
- Implement network segmentation
- Use read-only filesystems where possible
- Sign images with Docker Content Trust

### Performance
- Use multi-stage builds to reduce image size
- Minimize the number of layers
- Group related commands in a single RUN instruction
- Remove unnecessary files in the same layer they were added
- Use .dockerignore to exclude files from the build context
- Leverage build cache efficiently
- Use appropriate base images (Alpine for smaller size)
- Optimize Dockerfile ordering (place changing instructions last)

### Development
- Use docker-compose for local development
- Mount source code as volumes for rapid iteration
- Use environment variables for configuration
- Document your Dockerfile with comments
- Version your images with meaningful tags
- Implement hot-reloading for development containers
- Use development-specific Docker Compose overrides
- Set up consistent development environments across the team

### Production
- Use specific image tags, not "latest"
- Implement health checks
- Set resource limits
- Use orchestration tools (Kubernetes, Docker Swarm)
- Implement logging and monitoring
- Create a CI/CD pipeline for image building and deployment
- Use container registries with vulnerability scanning
- Implement proper backup and restore procedures
- Use production-ready base images
- Implement proper container lifecycle management

## Troubleshooting

### Common Issues

#### After $docker image or $docker image ls you get "error during connect: Head "http://%2F%2F.%2Fpipe%2FdockerDesktopLinuxEngine/_ping": open //./pipe/dockerDesktopLinuxEngine: The system cannot find the file specified"
```bash
# Just turn on Docker Desktop
```

#### Container won't start
```bash
# Check container logs
docker logs [container_id/name]

# Inspect container
docker inspect [container_id/name]

# Check if ports are already in use
netstat -tuln
```

#### Image won't build
```bash
# Check Dockerfile syntax
# Ensure build context is correct
# Verify internet connectivity for external resources
```

#### Network connectivity issues
```bash
# Check network configuration
docker network inspect [network_name]

# Test network from inside container
docker exec [container_id/name] ping [destination]

# Check if container's DNS is working
docker exec [container_id/name] nslookup google.com
```

#### Volume permission issues
```bash
# Check ownership and permissions
# Ensure correct paths are specified
# Use chown in Dockerfile or at runtime
```

#### Resource constraints
```bash
# Check system resources
docker stats

# Increase container resource limits
docker update --cpus [value] --memory [value] [container_id/name]
```

#### Docker daemon issues
```bash
# Restart Docker daemon
# Windows/Mac: Restart Docker Desktop
# Linux: sudo systemctl restart docker
```

---

This cheatsheet is a living document. Contributions and suggestions are welcome!
