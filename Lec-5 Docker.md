
# Virtual Machine

## Definition
A virtual machine is a software-based emulation of a physical computer that runs a complete operating system on top of host hardware.

## Architecture
- Hypervisor sits between hardware and VMs
- Each VM contains full guest OS
- Includes own kernel, binaries, and libraries
- Complete isolation from host system

## Types of Hypervisors
- Type 1 (Bare-metal): Runs directly on hardware (ESXi, Xen, Hyper-V)
- Type 2 (Hosted): Runs on host OS (VirtualBox, VMware Workstation)

## Characteristics
- Heavy resource consumption
- Gigabytes in size
- Boot time in minutes
- Strong isolation
- Can run different OS types simultaneously

## Use Cases
- Running multiple operating systems
- Testing and development environments
- Legacy application support
- Server consolidation
- Strong security isolation requirements

---

# Docker

## Definition
Docker is a platform for developing, shipping, and running applications in containers using OS-level virtualization.

## Core Concepts
- Containerization platform
- Uses Linux kernel features (namespaces, cgroups)
- Shares host OS kernel
- Lightweight compared to VMs

## Architecture Components
- Docker Engine: Core runtime
- Docker CLI: Command-line interface
- Docker Daemon: Background service
- Docker Registry: Image storage

## Benefits
- Consistent environments across development, testing, production
- Fast startup times (seconds)
- Efficient resource utilization
- Easy application deployment
- Version control for environments

## Container vs VM
- Containers share host kernel, VMs have separate kernels
- Containers are MB in size, VMs are GB
- Containers start in seconds, VMs in minutes
- Containers have process-level isolation, VMs have OS-level isolation

---

# Images

## Definition
An image is a read-only template containing everything needed to run an application: code, runtime, libraries, dependencies, and configuration.

## Image Layers
- Built in layers using union file system
- Each instruction in Dockerfile creates a new layer
- Layers are cached and reusable
- Only changed layers need to be rebuilt

## Image Structure
- Base layer: Usually an OS (Alpine, Ubuntu, Debian)
- Application layers: Dependencies and code
- Metadata: Configuration and runtime settings

## Image Naming Convention
```
repository:tag
example: nginx:latest
example: ubuntu:22.04
```

## Key Operations
- Pull: Download image from registry
- Build: Create image from Dockerfile
- Push: Upload image to registry
- Tag: Label images with versions
- List: View local images

## Image Storage
- Stored in Docker Registry (Docker Hub, private registries)
- Layers shared between images to save space
- Images are immutable once created

---

# Docker Installation

## Linux Installation

### Ubuntu/Debian
```bash
# Update package index
sudo apt-get update

# Install dependencies
sudo apt-get install ca-certificates curl gnupg lsb-release

# Add Docker's official GPG key
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Set up repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

### Arch Linux
```bash
sudo pacman -S docker docker-compose
```

## Post-Installation Steps

### Start Docker service
```bash
sudo systemctl start docker
sudo systemctl enable docker
```

### Add user to docker group
```bash
sudo usermod -aG docker $USER
```
Log out and back in for changes to take effect.

## Verify Installation
```bash
docker --version
docker info
```

## macOS Installation
- Download Docker Desktop from docker.com
- Install .dmg package
- Launch Docker Desktop application

## Windows Installation
- Download Docker Desktop for Windows
- Requires WSL 2 backend
- Install and restart system

---

# Docker Run Hello-World

## Command
```bash
docker run hello-world
```

## What Happens

### Step 1: Check Local Images
Docker checks if `hello-world` image exists locally.

### Step 2: Pull Image
If not found locally, Docker pulls from Docker Hub registry.

### Step 3: Create Container
Docker creates a new container from the image.

### Step 4: Execute
Container runs, prints message, and exits.

## Expected Output
```
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from Docker Hub.
 3. The Docker daemon created a new container from that image.
 4. The Docker daemon streamed that output to the Docker client.
```

## Verification Purpose
- Confirms Docker is installed correctly
- Tests Docker daemon connectivity
- Verifies Docker can pull images from registry
- Validates container execution capability

## Behind the Scenes
- Image pulled: `library/hello-world:latest`
- Container created with random name
- Container exits after printing message
- Container remains on system (stopped state)

## Cleanup
```bash
# View all containers
docker ps -a

# Remove stopped container
docker rm <container_id>

# Remove image
docker rmi hello-world
```

---

# Docker Hub

## Definition
Docker Hub is the official public registry for Docker images, serving as a centralized repository for container images.

## Key Features
- Public and private repositories
- Official images from software vendors
- Community-contributed images
- Automated builds from GitHub/Bitbucket
- Webhooks for integration
- Team collaboration tools

## Repository Types

### Official Images
- Maintained by Docker and vendors
- Verified and secure
- No username prefix (nginx, ubuntu, postgres)
- Regular security updates

### Community Images
- Created by users and organizations
- Format: `username/image-name`
- Varying quality and maintenance

## Basic Operations

### Pull Image
```bash
docker pull nginx
docker pull ubuntu:22.04
docker pull username/custom-image
```

### Push Image
```bash
# Tag image
docker tag local-image username/repository:tag

# Login to Docker Hub
docker login

# Push image
docker push username/repository:tag
```

### Search Images
```bash
docker search nginx
```

## Image Tags
- Version identifiers for images
- `latest` tag refers to most recent version
- Semantic versioning common (1.0.0, 2.1.3)
- Multiple tags can point to same image

## Best Practices
- Use official images when possible
- Specify exact version tags in production
- Review image security and maintenance
- Check number of pulls and stars
- Read image documentation
- Scan images for vulnerabilities

## Alternatives
- Google Container Registry (GCR)
- Amazon Elastic Container Registry (ECR)
- Azure Container Registry (ACR)
- GitLab Container Registry
- Self-hosted registries (Harbor)

---

# Daemon

## Definition
The Docker daemon (dockerd) is a persistent background process that manages Docker containers, images, networks, and volumes on a host system.

## Architecture Role
- Server-side component of Docker Engine
- Listens for Docker API requests
- Manages Docker objects (containers, images, networks, volumes)
- Communicates with other daemons for swarm services

## Core Responsibilities
- Building images from Dockerfiles
- Pulling images from registries
- Creating and running containers
- Managing container lifecycle
- Handling networking between containers
- Managing storage volumes
- Monitoring container health

## Communication
- Docker CLI sends commands to daemon via REST API
- Can listen on Unix socket (default: `/var/run/docker.sock`)
- Can listen on TCP socket for remote connections
- Uses HTTP/HTTPS protocols

## Service Management

### Start daemon
```bash
sudo systemctl start docker
```

### Stop daemon
```bash
sudo systemctl stop docker
```

### Enable on boot
```bash
sudo systemctl enable docker
```

### Check status
```bash
sudo systemctl status docker
```

## Configuration
- Configuration file: `/etc/docker/daemon.json`
- Settings: storage driver, logging, registry mirrors
- Restart required after configuration changes

## Logs and Debugging
```bash
# View daemon logs
sudo journalctl -u docker

# Check daemon info
docker info
```

## Security Considerations
- Runs with root privileges
- Socket access grants full Docker control
- Use TLS for remote connections
- Implement proper access controls
- Regular security updates essential

## Client-Daemon Model
- Docker CLI is the client
- Daemon is the server
- Client and daemon can be on different systems
- Multiple clients can connect to one daemon
```