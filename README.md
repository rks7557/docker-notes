# docker-notes
Docker daily notes...

Docker is a platform for building, packaging, and running applications inside containers.

A container is a lightweight, isolated environment that includes:

your application code
runtime (like Node.js, Python, Java)
libraries and dependencies
configuration files

This makes the app run the same way on:

your laptop
a testing server
production/cloud environments
Simple analogy

Think of Docker containers like shipping containers for software.

No matter where the container goes — ship, truck, or train — the contents stay consistent.

Similarly, Docker ensures:

“It works on my machine” → also works everywhere else.

# history -
 dot cloud named company saw this fir, that "it works on my machine but not working in client's machine" & then they resolve it in 2013 and in a public conference named "pyconn" in they opensource it and it a CNCF (Cloud native computing foundation) certified.

 # Why using docker ?

 The software industry started widely adopting Docker because modern applications became harder to build, deploy, and scale consistently across different environments.

Before Docker, teams faced major problems:

apps worked on one machine but failed on another.
deployments were slow and unreliable.
servers were hard to manage.
scaling applications required lots of manual work.
virtual machines were heavy and resource-intensive.

# Difference between virtualization and containerization

# Virtualization

. Virtualization (Virtual Machines)

In virtualization, a physical computer is divided into multiple virtual machines (VMs).

Each VM has:

its own full operating system
virtual hardware
applications

A software layer called a hypervisor manages them.

Common VM tools:

VMware
VirtualBox
Hyper-V

Physical Server
│
├── Hypervisor
│   ├── VM 1 → Ubuntu + App
│   ├── VM 2 → Windows + App
│   └── VM 3 → CentOS + App


# Containerization

Containerization

In containerization, applications share the host operating system kernel.

Containers only package:

application code
libraries
dependencies

They do NOT contain a full operating system.

Most popular tool:

Docker

Physical Server
│
├── Host Operating System
│
├── Docker Engine
│   ├── Container 1 → App
│   ├── Container 2 → App
│   └── Container 3 → App

# Docker components :-

Docker uses client-server architecture.

1. Docker client - It interacts with Docker Daemon that performs running, heavy lifting of building & distribution of docker containers.

2. Docker host - This is the actual machine where docker is installed.
Docker Daemon runs --> step 1
Images are created --> step 2
Containers are created --> step 3

3. Docker Daemon - The Docker Daemon is the docker engine/background service.
It listen the command and then perform like :-
 --> Download image
 --> Create container
 --> Start container

4. Images - It is like a template or Blueprint.
They are read-only, reusuable and stored locally after download.

ex - ubuntu, nginx, redis, mysql.

5. Containers - It is the running instance of the image.

ex - nginx image ---> nginx running container.

6. Registry - It is an online image store location.

ex - docker hub.

It stores images like -- nginx, mysql, ubuntu, redis

Complete Flow with commands  :-

a. docker pull nginx

Client --> Docker Daemon --> Docker hub registry --> Download nginx image --> Stores image locally

b. docker run nginx 

docker checks local image
if image exist locally then create container, otherwise pull the image first from the docker hub afterthat start the container.

c. docker build -t <image_name>: v1 .

If we build our own application image.
Ex :-
We made a Dockerfile

FROM nginx:alpine

COPY index.html /usr/share/nginx/html/index.html

Dockerfile --> Docker build image --> Stores image locally --> Can run container from it.

# Real world example :-

Image -- Cake recipe

Container -- Actual cake

Registry -- Recipe website

Docker Daemon -- chef

Docker run -- Bake cake

Docker pull -- Download recipe

# Installation in Docker

sudo dnf remove docker docker-client

sudo dnf install -y yum-utils

sudo dnf config-manager --add -repo https://download.docker.com/linux/centos/docker-ce.repo

sudo dnf install -y docker-ce docker-ce-cli containerd.io

sudo systemctl enable --now docker

sudo usermod -aG docker $USER

sudo firewall-cmd --permanent --add-port=8080/tcp && sudo firewall-cmd --reload

sudo run -d -p 8080:80 --name my-web-server nginx

docker ps

