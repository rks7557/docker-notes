# docker-notes
Docker daily notes, made by "Rishav Kumar Sharma"

Docker is a platform for building, packaging, and running applications inside containers.

A container is a lightweight, isolated environment that includes:

your application code runtime (like Node.js, Python, Java) libraries and dependencies configuration files.

This makes the app run the same way on:

your laptop a testing server production/cloud environments Simple analogy.

Think of Docker containers like shipping containers for software.
No matter where the container goes — ship, truck, or train — the contents stay consistent.

Similarly, Docker ensures:

“It works on my machine” → also works everywhere else.

# history -
 dot cloud named company saw this first time, that "it works on my machine but not working in client's machine" & then they resolve it in 2013 and in a public conference named "pyconn" in they opensource it and it a CNCF (Cloud native computing foundation) certified.

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


# Docker Networking

Docker networking is the system that allows:

Containers to communicate with each other
Containers to communicate with the host machine
Containers to access the internet
External users to access applications running inside containers

# Why needed Docker networking ??

Imagine you have:

One container running a frontend app
One container running a backend API
One container running MySQL database

How will they talk to each other?

Docker networking solves this.

Real world example :-
	
Container       -->	      Employee

Docker Network   -->	Office LAN

IP Address	     --> Employee desk extension

Port Mapping  -->	Reception desk forwarding calls

Bridge Network -->	Department network

Host Network  -->	Employee directly using company main phone

Overlay Network  -->	Multiple branch offices connected

DNS in Docker -->	Employee directory system

# How Docker Networking Works Internally

Every container gets:

Its own network namespace

Virtual Ethernet interface (veth)

IP address

Routing table

DNS configuration

#Docker creates:

a) Virtual bridges
b) NAT rules using iptables
c) Internal DNS server

# Types of Docker Networks

Main Docker network drivers:

a) Driver	Purpose
b) bridge	Default communication
c) host	Share host networking
d) none	No networking
e) overlay	Multi-host container communication
f) macvlan	Container gets real LAN IP

# Bridge Network

This is the default Docker network.

When you install Docker, Docker automatically creates.

docker network ls

you'll see output like :-
bridge
host
none

#Real-World Example of Bridge Network

Imagine:

HR department has its own internal network, Employees inside HR can communicate easily and other departments cannot directly access.

Same happens in Docker bridge network.

How Bridge Network Works

When container starts:

a) Docker assigns private IP
b) Connects container to virtual bridge (docker0)
c) Creates NAT rules.

Example :-

docker run -dit --name c1 nginx
docker run -dit --name c2 ubuntu

check ip:

docker inspect c1

Communication flow :-

Container1 → docker0 bridge → Container2

note :- if we run command ip addr we will see output like docker0, which behaves like a virtual switch.

Problem in Default Bridge

Containers cannot resolve names automatically.

Example:

c1 cannot ping c2 by container name

Solution:

Use custom bridge network.

# Custom Bridge Network

Create:

docker network create mynet

Run containers:

docker run -dit --name app1 --network mynet nginx

docker run -dit --name app2 --network mynet ubuntu

Now:

ping app1

Works because Docker provides internal DNS.

Real-World Example

Like company internal employee directory:

You call “Rahul”, instead of extension number.

Docker DNS does same.

# Port Mapping

Containers are isolated.

Outside world cannot access container directly.

We expose ports using:

-p hostPort:containerPort

Example:

docker run -d -p 8080:80 nginx

Meaning:

Host	Container
8080	80

Now browser accesses:

http://localhost:8080
Real-World Example

Think:

Company receptionist receives calls on main number, Forwards to employee extension

Port mapping works same way.

Traffic Flow

User → Host Port 8080 → Container Port 80

# Host Network

Container shares host network directly.

Run:

docker run --network host nginx

Now container uses:

a) Host IP

b) Host ports directly

c) No isolation.

Real-World Example

Employee directly uses company main phone number.

No receptionist forwarding needed.

Advantages

Faster

No NAT

Better performance

Disadvantages

Less secure

Port conflicts possible

None Network

Container gets no networking.

Example:

docker run --network none ubuntu

Container:

No internet

No communication

Only loopback interface

Real-World Example

Employee locked in isolated room with no phone/network.

Overlay Network (Docker Swarm)

Used in multi-host environments.

Suppose:

Server	Container

Server1	frontend

Server2	backend

Server3	database

Overlay network connects them.

# Real-World Example

Different office branches connected via VPN/MPLS.

Employees communicate as if same office.

Create Overlay Network

docker network create -d overlay myoverlay

Requires Docker Swarm.

Architecture

Datacenter 1 ---- Overlay ---- Datacenter 2

Macvlan Network

Container gets actual LAN IP.

Example:

Router sees container as separate machine.

# Real-World Example

Employee gets personal direct phone number instead of extension.

Use Cases

Legacy applications

Monitoring tools

Network appliances

Container DNS Resolution

Docker has built-in DNS server.

Containers can communicate using:

Container names

Service names

Example:

ping mysql

instead of:

ping 172.18.0.5

# Inspect Docker Network

Command:

docker network inspect bridge

Shows:

Subnet

Gateway

Connected containers

IP allocation

Connect Running Container to Network

docker network connect mynet container1

Disconnect:

docker network disconnect mynet container1

Real Production Example

Suppose you deploy:

Service	Container

React App	frontend

NodeJS API	backend

MySQL	database

Redis	cache

#Create network:

docker network create prod-net

Run:

docker run -d --name mysql --network prod-net mysql

docker run -d --name redis --network prod-net redis

docker run -d --name backend --network prod-net backend-image

docker run -d --name frontend --network prod-net frontend-image

Backend accesses:

mysql:3306
redis:6379

using container names.

How Internet Access Works

Container traffic:

Container → docker0 → Host NAT → Internet

Docker uses:

iptables

masquerading/NAT

# Important Commands

List Networks

docker network ls

Create Network

docker network create mynet

Inspect Network

docker network inspect mynet

Remove Network

docker network rm mynet

Connect Container

docker network connect mynet c1

# Docker Compose Networking

Example:

services:
  frontend:
    image: nginx

  backend:
    image: nodeapp

  db:
    image: mysql

Docker Compose automatically creates network.

# Services communicate using:

frontend, backend & db as hostnames.

# Security Best Practices
Use Custom Bridge Networks

Avoid default bridge.

Do Not Expose Database Ports Publicly

Bad:

-p 3306:3306
Use Internal Networks

Frontend exposed:
Backend and DB internal only.