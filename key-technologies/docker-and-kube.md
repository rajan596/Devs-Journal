# Docker

### Solves the problem of 
- Consistent environment. Works on my machine but not on prod env. Package differences of language, operating system etc


### 3 main components
1. Dockerfile (Blueprint): step by step instructions to build docker container
2. Image: When we run `docker build` it executes instructions step by step defined in Dockerfile and it creates an image
3. Container: when we run `docker run` on an image it creates a container. A container is a live running instance of an image.

### Virtual Machine vs Container
- Virtual Machines : Emulates a full computer, including its own OS kernel, on top of a hypervisor; each VM is like a separate server with its own OS, libraries, and apps
- Containers - shares the OS kernerl, can spin up in seconds.
    - In one VM we can have multiple containers


| Aspect              | Virtual Machine                             | Container                                             |
| ------------------- | ------------------------------------------- | ----------------------------------------------------- |
| What is virtualized | Hardware (full machine)                     | Operating system (user space)                         |
| OS per instance     | Each VM has its own OS                      | All containers share host OS kernel                   |
| Resource usage      | Heavy (GBs disk, more CPU/RAM)              | Lightweight (hundreds of MB, lower CPU/RAM)           |
| Startup time        | Slow (seconds to minutes, full OS boot)     | Fast (usually seconds)                                |
| Isolation           | Strong, hardware-level; better for security | Weaker, kernel shared; good enough for many workloads |
| Portability         | Less portable; bigger images                | Very portable; ideal for CI/CD and microservices      |


# Kubernetes

- Docker container across the fleets needs to be managed for
    - Server crash -> shift all containers to new server
    - If one container dies -> restart it
    - New code update -> deploy new ones and kill previous all containers
- That means someone is required to keep an eye on all containers and manage them. This is where **Kubernetes** comes to the rescue