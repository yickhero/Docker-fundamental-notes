# Docker-fundamental-notes
Here are my notes after finish learning the fundamentals of Docker
# Comprehensive Docker Fundamentals

## What is Docker?

* It is a virtualization software which makes developing and deploying applications much easier.
* It does that by packaging the application into a container with all the necessary dependencies, configurations, system tools, and runtime.
* This makes it easy to share and distribute.

## Why Docker?

### Development Before Docker

* Each developer needed to install and configure all services directly on their OS on their local machine.
* The installation process could be different for different operating systems, and things can go wrong when installing them (e.g., PostgreSQL, Redis).

### Development With Docker

* You have all the services you need in an isolated environment with all dependencies and configs.
* You can just start a service as a Docker container by using a single Docker command.
* It then fetches the container packages from the internet and starts it on your computer.
* The command will be exactly the same for all operating systems and all services.

### Deployment Benefits

* **Before:** Deployment was rough when the developers and operations team had to figure out how to set everything up on the server.
* **Now:** Developers create an application package that includes the code, dependencies, and all configurations packaged inside the Docker artifact.
* No configurations are needed on the server (except the Docker runtime).
* The Operations team only has to run the Docker command to fetch and run the Docker artifacts.

---

## Docker VS Virtual Machine

To understand the difference, it helps to know how an OS is made up:

1. **OS Kernel:** At the core of every OS; it interacts between hardware and software components.
2. **OS Application Layer:** On top of the OS kernel layer where it runs applications.

| Feature | Docker Containers | Virtual Machines (VM) |
| --- | --- | --- |
| **Virtualization Level** | Virtualizes only the OS application layer (as well as services/apps installed on top). | Virtualizes the OS kernel *and* the OS application layer. |
| **Kernel Usage** | Uses the kernel of the host machine. | Runs its own completely separate kernel. |
| **Size** | Docker packages/images are much smaller. | VM images are massive (often gigabytes). |
| **Speed** | Takes a much shorter time to start. | Takes longer to boot up. |
| **OS Compatibility** | Natively compatible only with Linux distros. | Can run any OS. |

> **Note on Compatibility:** Docker Desktop makes it possible for Linux containers to run on Windows or MacOS by using a hypervisor layer with a lightweight Linux distro.

---

## Docker Desktop Ecosystem

Docker Desktop includes three main components:

1. **Docker Engine:** A server with a long-running daemon process called `dockerd` which manages images and containers.
2. **Docker CLI (Client):** A command line interface (`docker`) to interact with the Docker Server. You execute Docker commands here to start, stop, and manage containers.
3. **GUI Client:** Other than commands, you can manage your containers and images with a graphical user interface.

---

## Images, Containers & Registries

### What are Docker Images?

* An executable application artifact.
* It includes app source code, but also the complete environment configuration.
* You can add environment variables, create directories, files, etc.
* It acts as an immutable template that defines how a container will be realized.
* **Command to check all images:**
```bash
docker images

```



### What are Docker Containers?

* A container actually starts the application.
* It is a running instance of an image (when the container environment is created).
* You can run multiple containers from 1 image.
* **Note:** The `docker run` command actually creates a *new* container every time and it doesn’t reuse previous containers.

**Essential Container Commands:**

* List running containers: `docker ps`
* List all previous/stopped containers: `docker ps -a`
* Create a container from a given image and start it: `docker run -d {name}:{tag}`
* Assign a specific name when creating a container: `docker run --name web-app -d -p 9000:80 nginx:1.23`
* Reuse/start a previously run container: `docker start {container}` *(You can use the container ID or the assigned name as a reference).*
* Stop a container: `docker stop {container}`
* Check container logs: `docker logs {container}`

### What are Docker Registries?

* A storage and distribution system for Docker images.
* Official images are available from applications like Redis, Mongo, Postgres, etc. These are maintained by the software authors or in collaboration with the Docker community.
* Docker hosts one of the biggest Docker registries, called **Docker Hub**.
* **Command to pull an image:**
```bash
docker pull {name}:{tag}

```



#### Docker Registry VS Repository

* **Docker Registry:** A service providing storage. It can be hosted by a third party (like AWS or Docker Hub) or by yourself. It is a collection of repositories.
* **Docker Repository:** A collection of related images with the exact same name but different versions/tags.

---

## Networking: How to Access a Container

* The application is inside a container that runs in an isolated Docker network.
* We need to expose the container port to the host (the machine the container runs on).
* We need to do **port binding**, which means binding the container’s port to the host’s port to make the service available to the outside world.
* Add the `-p` flag to the run command:
```bash
docker run -d -p 9000:80 nginx:1.23

```


*This will bind port `80` from the original nginx container to our local host port `9000`.*
* You can then access it through your browser at `localhost:9000`.

---

## How to Build a Docker Image

We need to create a "definition" of how to build an image from our application.

* A **Dockerfile** is a text document that contains commands to assemble an image.
* Docker can then build an image by reading those instructions.

### Structure of a Dockerfile

```dockerfile
# 1. FROM
# A Dockerfile must begin with a FROM instruction.
# Builds this image from the specified base image.
FROM node:19-alpine

# 2. WORKDIR
# Sets the working directory for all following commands.
# Similar to changing into a directory using "cd ...".
WORKDIR /app

# 3. COPY
# Copies files or directories from <src> and adds them to the container filesystem at <dest>.
# Note: While "RUN" is executed IN the container, "COPY" is executed ON the host.
COPY package.json /app/
COPY src /app/

# 4. RUN
# Will execute any command in a shell inside the container environment during the build.
RUN npm install

# 5. CMD
# The instruction that is to be executed when a Docker container starts.
# There can only be one "CMD" instruction in a Dockerfile.
CMD ["node", "server.js"]

```

### Build Commands

* To build a Docker image in the terminal:
```bash
docker build {path}

```


* To set a name and (optionally) a tag in the `{name}:{tag}` format, use the `-t` flag:
```bash
docker build -t {name}:{tag} {path}

```