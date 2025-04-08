# Docker Cheatsheet

A comprehensive reference guide for Docker commands, concepts, and best practices.

## Table of Contents
- [Installation](#installation)
- [Basic Concepts](#basic-concepts)
- [Docker Commands](#docker-commands)
  - [Images](#images)
  - [Containers](#containers)
  - [Volumes](#volumes)
  - [Networks](#networks)
  - [Docker Compose](#docker-compose)
- [Dockerfile Reference](#dockerfile-reference)
- [Best Practices](#best-practices)
- [Troubleshooting](#troubleshooting)

## Installation

### Windows
```bash
# Download Docker Desktop from https://www.docker.com/products/docker-desktop
# Follow the installation wizard
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
```

## Basic Concepts

### Images
- **Docker Image**: A read-only template containing instructions for creating a Docker container
- **Base Image**: A parent image that provides the foundation for other images
- **Official Image**: Curated and maintained by Docker
- **Docker Hub**: A registry service for Docker images

### Containers
- **Docker Container**: A runnable instance of a Docker image
- **Container Lifecycle**: Created, Running, Paused, Stopped, Deleted
- **Ephemeral**: Containers are designed to be temporary and disposable

### Volumes
- **Docker Volume**: Persistent data storage that exists outside of containers
- **Bind Mount**: Maps a host file or directory to a container file or directory
- **tmpfs Mount**: Stores data in memory only

### Networks
- **Bridge Network**: Default network driver for containers on the same host
- **Host Network**: Removes network isolation between container and host
- **Overlay Network**: Connects multiple Docker daemons and enables swarm services
- **Macvlan Network**: Assigns a MAC address to a container, making it appear as a physical device

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

## Best Practices

### Security
- Run containers with non-root users
- Use official images from trusted sources
- Regularly update base images
- Scan images for vulnerabilities
- Use secrets management for sensitive data
- Limit container capabilities and resources

### Performance
- Use multi-stage builds to reduce image size
- Minimize the number of layers
- Group related commands in a single RUN instruction
- Remove unnecessary files in the same layer they were added
- Use .dockerignore to exclude files from the build context

### Development
- Use docker-compose for local development
- Mount source code as volumes for rapid iteration
- Use environment variables for configuration
- Document your Dockerfile with comments
- Version your images with meaningful tags

### Production
- Use specific image tags, not "latest"
- Implement health checks
- Set resource limits
- Use orchestration tools (Kubernetes, Docker Swarm)
- Implement logging and monitoring
- Create a CI/CD pipeline for image building and deployment

## Troubleshooting

### Common Issues

#### After $docker image or $docker image ls you get "error during connect: Head "http://%2F%2F.%2Fpipe%2FdockerDesktopLinuxEngine/_ping": open //./pipe/dockerDesktopLinuxEngine: The system cannot find the file specified"
```bash
# Just turn on Docker Desktop
```
---

This cheatsheet is a living document. Contributions and suggestions are welcome!
