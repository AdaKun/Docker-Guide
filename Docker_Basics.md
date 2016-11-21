# Linux Academy: The Basics of Docker
My Notes for Docker, through Linux Academy

> ## 1. Docker Basics

## 2. [Dockerfile, Builds & Network Configurations](Dockerfile_Builds_Networks.md)
## 3. [Docker Commands & Structures](Docker_Commands_Structures.md)
## 4. [Docker Integration and Use Cases](Docker_Integration.md)

## Table of Contents
- [Linux Academy: The Basics of Docker](#linux-academy--the-basics-of-docker)
  * [Introduction to Docker](#introduction-to-docker)
    + [What is Docker](#what-is-docker)
    + [When to use docker](#when-to-use-docker)
  * [Containers Vs. Virtual Machines](#containers-vs-virtual-machines)
    + [Whats the difference](#whats-the-difference)
  * [Docker Architecture](#docker-architecture)
    + [Container architecture](#container-architecture)
    + [Docker client & daemon communications](#docker-client-and-daemon-communications)
    + [Docker Components](#docker-components)
    + [Docker is not a new idea](#docker-is-not-a-new-idea)
  * [The Docker Hub](#the-docker-hub)
  * [Docker Installation](#docker-installation)
    + [Creating the Docker Repository](#creating-the-docker-repository)
    + [The Permissions Problem](#the-permissions-problem)
    + [The Permissions Fix](#the-permissions-fix)
  * [Creating Our First Image](#creating-our-first-image)
    + [Quick overview of Dockers internal architecture](#quick-overview-of-dockers-internal-architecture)
      - [Docker image and container directory](#docker-image-and-container-directory)
    + [Pulling our first image](#pulling-our-first-image)
  * [Working With Multiple Images](#working-with-multiple-images)
  * [Packaging A Customized Container](#packaging-a-customized-container)
    + [Initial steps:](#initial-steps)
    + [Two methods:](#two-methods)
      - [Method 1, Creating an image by committing changes of a container](#method-1-creating-an-image-by-committing-changes-of-a-container)
      - [Method 2, Creating an image from a Dockerfile](#method-2-creating-an-image-from-a-dockerfile)
  * [Running Container Commands With Docker](#running-container-commands-with-docker)
      - [Find logs of a container while or when it is running](#find-logs-of-a-container-while-or-when-it-is-running)
      - [Execute a command on a container without attaching](#execute-a-command-on-a-container-without-attaching)
      - [Run commands using docker run](#run-commands-using-docker-run)
      - [You can also execute a command as a daemon so it continues to run in that container](#you-can-also-execute-a-command-as-a-daemon-so-it-continues-to-run-in-that-container)
  * [Exposing Our Containers With Port Redirects](#exposing-our-containers-with-port-redirects)

## Introduction to Docker

### What is Docker
Tool or set of tools that packages applications and it dependencies on a virtual container to any Linux systems or distributions

### When to use docker
 - Configure Simplification
 - Developer Productivity
 - Server Management 
 - Application Isolation
 - Rapid Deployment
 - [Primary Usage] Build Management Automation (e.g. Ansible, Jenkins)

## Containers Vs. Virtual Machines
VM's - Allows the user to build operating systems within their current system (shares physical machine resources & hardware)
 - VMWare, Citrix, VirtualBox, OpenStack, KVM
 - Usually X86 system 
 - communicates through a Hypervisor  

Containers - An isolated set of packages, libraries, applications. 

### Whats the difference
VM - Creates a full operating system stack, uses more system resources regardless of applications used

Container - Shares the operating system platform (can choose not to) and allows more granular management of resources based on use.   
<sub>_(e.g. user can contain a working web app like nginx to be used on top of a variety of Linux distribution / systems)_</sub>

## Docker Architecture

### Container architecture
   - Is a client-server application
   - Run as a [Same System] Docker client + Docker daemon
   - Or a [Remote System] Docker client > Remote Docker daemon
   - Runs on top of a single Linux instance
   - Docker engine = encapsulating process
   - Removes a whole layer from the VM hypervisor process
      + saves resources and space
    
### Docker client and daemon communications
   - Sockets
   - RESTful API (Representational State Transfer - Stateless transfer over HTTP of a web page containing an XML file)
    
### Docker Components
   - Daemon
   - Client
   - Docker.io Registry
    
### Docker is not a new idea
Docker has captured the right buzz
Other Applications Include:
   - FreeBSD - Jails
   - Sun (Oracle) Solaris - Zones
   - Google - lmcfy (Let Me Contain That For You)
   - OpenVZ
     
## The Docker Hub
   - Public registry/repository
   - Maintained by Docker Inc.
   - Contains images to build containers
       + pull images from cli to build containers
   - provides image information
       + Dockerfile
       + Variables
       + Configurations
   - Free to join
       + 1 single private repository
       + Unlimited public repositories
    
## Docker Installation

What we are using
```
CentOS 7
```
The CentOS default repo for Docker tends to be behind several builds so we are going to pull the latest docker from thier official repo.

### Creating the Docker Repository

Move to repo directory `cd /etc/yum.repos.d/`

View repo's `ll`

Create repo file `sudo vim docker.repo`

Type the following in the repo file
```
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/$releaserver/
enabled=1
gpgcheck=1
gpg=https://yum.dockerproject.org/gpg
```        
Update the cache and latest updates `sudo yum -y update`

Install docker from the docker repo `sudo yum -y install docker-engine`

Enable & Start Docker
```
sudo systemctl enable docker
sudo systemctl start docker
```
Verfiy Docker `sudo systemctl status docker`

Verify Docker images `docker images`

### The Permissions Problem

you should get

```diff
- ("Cannot connect to the Docker daemon.")
```

Why does this happen?

By default docker installs docker.sock under the "docker" group

To see what we are talking about:
```
cd /var/run
ls -al dock*
```
<sub>_Only Root or docker group can access Docker because of docker.sock permissions_</sub>

### The Permissions Fix

Add our user to the docker group `sudo usermod "user" -G docker`

Logout & Login to enact user modification
```
exit
su "user"
```
Reinsure docker is working properly `docker run hello-world`

Verify latest docker version `docker --version`

## Creating Our First Image

Goal: to pull an image and represent an instance or container from it

### Quick overview of Dockers internal architecture

Two components to a docker infrastructure:

1. An image (of anything)
2. Containers (instance of an image run as a container)

Be sure version of Docker is at least 1.1 `docker version`

Find more information in Docker `docker info`

How much space can I use using Docker images?

Check disk space `df -h`

<sub>_(If /var/lib/docker is not mounted then the space available in your root (/) directory is what you are limited to.)_</sub>

#### Docker image and container directory

`/var/lib/docker`

<sub>_must be root to access this directory_</sub>

**Container location**

`/var/lib/docker/container`

View current containers `docker ps`

View past containers `docker ps -a`

Two ways to refer to a container:

1. "CONTAINER ID" (12 characters)
2. A Unique image name (e.g. cocky_elion)

**Image location**

`/var/lib/docker/image/devicemapper/imagedb/content/sha256/"image"`

You can view this image running `docker images`

### Pulling our first image

Through the *docker hub* we will pull the latest ubuntu image from their official repository using the Docker pull command

<sub>_remember, As non-root user under the docker group_</sub>

`docker pull ubuntu:xenial` 

<sub>_We could use `docker pull ubuntu` but we will be more specific with the "xenial" image even when new builds are released_</sub>

Check docker images `docker images`

Run the image in a container `docker run -i -t ubuntu:xenial /bin/bash`

| Command             | Description                      |
| ------------------- |:-------------------------------: |
| docker run          | Run a command in a new container |
| -i                  | run in interactive mode          |
| -t                  | attach to terminal/tty           |
| ubuntu:xenial       | docker image                     |
| /bin/bash           | interactive command              |

This will start you in a /bin/bash prompt in the container you just created

you can check the processes the container is running: `ps aux`

<sub>_you will see the ps and bin/bash proccess_</sub>

if you exit the container `exit`

You will notice the container is no longer running.

<sub>_exiting the contatiner also closes the container proccess_</sub>

You can find the last used containers and its unique names running a `docker ps -a`

You can also restart the container with `docker restart "unique-name"`

<sub>_but you'll also notice you will not have a /bin/bash prompt_</sub>

You can check if the image is running using a `docker ps`

You can check more information about the base image by running `docker inspect ubuntu:xenial`

You can return to the bash prompt of the container by running `docker attach "unique name"`

<sub>_the disadvantage of this is exiting the container will also exit the container process again_</sub>

## Working With Multiple Images

Exploring the different ways in using multiple images.

Recall before that our container would automatically attach itself to the bash prompt, this time we can suspend the container in the background as a daemon using the option `-d` (-d = disconnected)

e.g. `docker run -i -t -d ubuntu:xenial /bin/bash`

Checking any running Docker images using, `docker ps`

<sub>_you will notice that the container is runnning_</sub>

Running a `docker ps -a` you will also notice the container running is a completely different image from the last

You can also inspect the container with a `docker inspect "unique name"`. Information is presented in JSON
Important configurations include:

* TTY connection
* environment variable
* command
* image
* gateway
* ip

<sub>_can also inspect stopped containers_</sub>

<sub>_One possible issue with Docker is the difficulty in controlling a static IP address_</sub>

You can run a `docker stop "unique_name"` to stop a container

You can use `docker search "name"` to search for all images in Docker Hub

Tips:

* you can run multiple instances of a container
* you can choose to start and stop different instances
* restarting an instance may reset configurations and apps installed unless the base image already had it.

## Packaging A Customized Container

Goal: The ability to carry forward changes made into a container

Base image
container based upon that image
any changes within containers do not affect base image
changes cannot be carried forward to other containers made from the base image
changes within a container are persistent within, as long as the image remains on the system and can restart it.

When you may want a container you configured and be able to build other containers with the same configurations

### Initial steps

1. create a container from a base image
2. make changes / install packages
3. create a new base image

Check if images are running `docker ps`

Check images on system that have run before `docker ps -a`

start over with new image container `docker run -it ubuntu:xenial /bin/bash`

In container

Go to root `cd /root`

write a generic text file `echo "this is version 1 of our custom image" > image_ver.txt`

run an update `apt-get update`

Install telnet and SSH `apt-get  -y install telnet openssh-server`

<sub>_This will give us a container with services, system configurations and the ability to add users and connect to the container without stopping the container_</sub>

Add user `adduser "test"`

<sub>_fill in password and full name_</sub>

Check changes

```
which sshd
which telnet
cat /etc/group | grep test
```

Exit container `exit`

Grab unique container name using `docker ps -a`

Restart & attach to container

```
docker restart "unique_name"
docker attach "unique_name"
```

Check for custom text file

```
cd /root
ll
```

<sub>_You will see the text file you created there, on that note that file will not show on any other container based on the image._</sub>

How do we get our text file, telnet, user and ssh to the base image?

### Two methods

1. start a container, make changes and commit the container
2. create a Dockerfile

#### Method 1 Creating an image by committing changes of a container

We need to commit it!
`docker commit -m "Already installed SSH and created test user" -a "maintainer" container_name maintainer/ubusshd:v1`

Command break-down:

| Command               | Description                                                |
| --------------------- |:----------------------------------------------------------:|
| docker commit         | commit changes of a container to an image                  |
| -m                    | message/comment                                            |
| -a                    | author (e.g., "John Hannibal Smith <hannibal@a-team.com>") |
| image_name            | Unique_name of the container you're trying commit          |
| maintainer/ubusshd:v1 | [REPOSITORY[:TAG]]                                         |

You can now see the image when running `docker images`

Run the docker image `docker run -it maintainer/ubusshd:v1 /bin/bash`

In the container you should now see the same text file, telnet, sshd and user created in the previous container carried to this container

```
cd /root
ls -al
which telnet
which sshd
cat /etc/group | grep test
```

#### Method 2 Creating an image from a Dockerfile

This file will have information of: 
who we are
what base image it is from
and install some basic packages

Create a directory `mkdir build`
Move to directory `cd build`
Create Dockerfile `vim Dockerfile`

<sub>_the "d" in Dockerfile must be capital by default_</sub>

In vim

```
# This is a custom ubuntu image with SSH already installed
FROM ubuntu:xenial
MAINTAINER tcox <tcox@linuxacademy.com>
RUN apt-get update
RUN apt-get install -y telnet openssh-server
```

<sub>_This is the bare minimum, but here you can typically start service, expose port / volumes, link containers and much more_</sub>
<sub>_also be sure to add -y to your apt-get command, any question prompt that's not answered from a command will make docker abort the build_</sub>

Write the Dockerfile `docker build -t="tcox/ubusshdonly:v2" .`

<sub>_you can also redirect a dockerfile 'e.g. `docker build -t="tcox/ubusshdonly:v2" "left-redirect-arrow" /location/Dockerfile_</sub>

| Command              | Description                                        |
| -------------------- |:--------------------------------------------------:|
| docker build         | Build an image from a Dockerfile                   |
| -t                   | Name and optionally a tag in the 'name:tag' format |
| .                    | current directory                                  |

<sub>_Docker will not automatically start the service, as it manages services differently_</sub>

Find the newly created image running `docker image`

Run the image `docker run -it tcox/ubusshdonly:v2 /bin/bash`

Check telnet and sshd like before `which telnet`, `which sshd`

Done!

## Running Container Commands With Docker

#### Find logs of a container while or when it is running

Find recently run containers `docker ps -a`

View logs of a particular container `docker logs "container_name"`

<sub>_This tells you what has been run from the container. It also gives insight of what is currently running in the container_</sub>

#### Execute a command on a container without attaching 

(e.g. `docker exec ecstatic_yonath /bin/cat /etc/profile`)

| Command              | Discription              |
| -------------------- |:------------------------:|
| docker exec          | To "execute"             |
| ecstatic_yonath      | Unique name of container |
| /bin/cat             | command to run `cat`     |
| /etc/profile         | File directory           |

<sub>_you should get a readout of the /etc profile_</sub>

To test this you can attach to the container `docker attach "container_name"`

Update your Linux Distro, do an `apt-get update` / `yum update`

Install file editor, do a `apt-get install vi` / `yum install vi`

Edit the /etc/profile file `vim /etc/profile`

At the bottom place a comment you would recognize `# What is the Cargo of the Docker Whale?` and save.

Exit and restart docker container `docker restart "container_name"`

Execute docker cat command earlier

<sub>_You should notice the comment you placed earlier in the file_</sub>

#### Run commands using docker run 

(e.g. `docker run ubuntu:xenial /bin/echo "Hello Container"`)

<sub>_should echo back "Hello Container"_</sub>

Doing a `docker ps -a` you'll see that a specific container opened just to run the echo command.
You can also run a `docker logs "container_name"` and see that command has run.

#### You can also execute a command as a daemon so it continues to run in that container 

(e.g. ` docker run -d ubuntu:xenial /bin/bash -c "while true;do echo HELLO;sleep 1;done"`)

| Command                                 | Description                                   |
| --------------------------------------- |:---------------------------------------------:|
| docker run                              | Run a command in a new container              |
| -d                                      | As a daemon (containerize command as a daemon)|
| ubuntu:xenial                           | Docker image name                             |
| /bin/bash -c                            | Execute a command                             |
| "while true;do echo HELLO;sleep 1;done" | Bash Script                                   |

You can check the if the script is running by running the `docker logs "container_name"`

<sub>_You'll notice a bunch of HELLO's are running_</sub>

You can also check if it is continuously running by checking the count of the number of lines `docker logs "container_name" | wc -l`

<sub>_You'll notice that the number will grow every time you type and run the command_</sub>

## Exposing Our Containers With Port Redirects

