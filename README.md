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

# Deploy custom application

make a directory and go to inside the directory

mkdir my-first-app && cd my-first-app

make an index.html

vi index.html

put some custom html file for exmaple, you can get it in chatpt

Create a Dockerfile, with name Dockerfile and put below content

FORM nginx:alpine

COPY index.html /usr/share/nginx/html/index.html

Build custom image

docker build -t my-custom-web:v1 .

Stop old nginx container first, then delete the existing nginx image

docker ps

docker stop container_id

docker rm -f container_id

docker images , with this command you will get the images

docker rmi image_name

Then run the container, 

docker run -d -p 8080:80 --name my-custom-container my-custom-web:v1


Docker Push & Pull from Docker Hub

I created a portfolio application using HTML, CSS, and JavaScript.

First, install Docker Desktop or log in to your Docker account from the browser and create a repository named portfolio.

Create two folders:

/tmp/portfolio
/my-portfolio

Step 1 — Copy Portfolio Code

Copy your portfolio application code from your local system to:

/tmp/portfolio

Step 2 — Move Code to Project Directory

Move all files from /tmp/portfolio to /my-portfolio.

Example:

mv /tmp/portfolio/* /my-portfolio/

Step 3 — Create Dockerfile

Create a file named Dockerfile inside /my-portfolio.

FROM nginx:alpine

COPY . /usr/share/nginx/html

Step 4 — Build Docker Image

Run the following command inside /my-portfolio:

docker build -t rks7557/portfolio:v1 .

Step 5 — Login to Docker Hub

docker login -u rks7557

Enter your Docker Hub password when prompted.

Step 6 — Push Docker Image to Docker Hub

docker push rks7557/portfolio:v1

Step 7 — Pull the Docker Image

docker pull rks7557/portfolio:v1

Step 8 — Run the Container

docker run -d -p 8080:80 --name my-portfolio rks7557/portfolio:v1


# Docker Volume

A docker volume is a persistant storage mechanism used to store data outside container filesystem.
Normally containers are temporary.

We use docker volume normally in 2 cases:-

1. If container is deleted

2. All internal data is lost.

Problem Without Volumes :-

Example

docker run -dit --name test alpine

Create file

echo "hello" > demo.txt

Now remove container:

docker rm -f test

Now from above example, everything got lost, as container filesystems are temporary.

Solution — Docker Volume

Volume keeps data separately.

Even if container deletes:
data survives.

Docker Volume Architecture :-

Container --->  Mounter Volume --->  Host Storage

Create a Docker Volume

docker volume create portfolio-data

Verify Volume

docker volume ls

Inspect Volume

docker volume inspect portfolio-data

Actual Data Location

Docker stores volume data here:

/var/lib/docker/volumes/

In my case 

/var/lib/docker/volumes/portfolio-data/_data

Use Volume with Container

-v volume-name:container-path

docker run -d  --name mynginx -v portfolio-data:/usr/share/nginx/html nginx

Anonymous Volumes

Docker can create unnamed volumes automatically.

Example:

docker run -v /data nginx

Docker creates random volume.

Not recommended for production.

Named Volumes 

Example:

docker volume create mysql-data

Bind Mount vs Volume

Bind Mount
-v /host/path:/container/path

Example:

-v /home/rishav/project:/app

Direct host folder mapping.

Docker Volume
-v volume-name:/container/path

tmpfs Mount

Stored in RAM only.

Example:

docker run --tmpfs /data nginx

Fast but temporary.

Used for:

secrets

cache

temporary files

Volume Drivers

Docker supports drivers.

Default:

local

Advanced:

NFS

cloud storage

plugins

Shared Volumes

Multiple containers can use same volume.

Example:

Container A
       ↓
Shared Volume
       ↑
Container B

Example

docker run -dit --name c1 -v shared-data:/data alpine

docker run -dit --name c2 -v shared-data:/data alpine

Both access same data.

Real World Database Example

Most important use case.

MySQL Persistent Storage

docker run -d --name mysql -e MYSQL_ROOT_PASSWORD=admin -v mysql-data:/var/lib/mysql mysql

Database stored safely.

Why Important?

Without volume:
❌ DB lost after container deletion

With volume:
✅ DB survives

Jenkins Example

docker run -d -p 8080:8080 -v jenkins-data:/var/jenkins_home jenkins/jenkins

Jenkins jobs remain persistent.

Backup Docker Volume

Simple Backup

tar -czvf backup.tar.gz /var/lib/docker/volumes/website-data

Restore

Extract back into volumes directory.

Remove Volume

docker volume rm website-data

Remove Unused Volumes

docker volume prune

Removes dangling volumes.

Volume Lifecycle

Create Volume
      ↓
Attach to Container
      ↓
Store Data
      ↓
Container Removed
      ↓
Volume Still Exists

Best Practices

✅ Use named volumes
✅ Use volumes for databases
✅ Backup important volumes
✅ Avoid anonymous volumes in production
✅ Separate application and data

