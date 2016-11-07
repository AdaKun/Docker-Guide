# Linux Acadeny Docker Personal Guide
My Notes for Docker, through Linux Academy

## Table of Contents
- [Introduction to Docker](#introduction-to-docker)
    + [What is Docker](#what-is-docker)
    + [When to use docker](#when-to-use-docker)
- [Containers Vs. Virtual Machines](#containers-vs-virtual-machines)
    + [Whats the difference](#whats-the-difference)
- [Docker Architecture](#docker-architecture)
    + [Container architecture](#container-architecture)
    + [Docker client & daemon communications](#docker-client---daemon-communications)
    + [Docker Components](#docker-components)
    + [Docker is not a new idea](#docker-is-not-a-new-idea)
- [The Docker Hub](#the-docker-hub)

## Introduction to Docker

#### What is Docker
Tool or set of tools that packages applications and it dependencies on a virtual container to any Linux systems or distrobutions

#### When to use docker
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
Container - An isolated set of packages, libraries, applications. 

#### Whats the difference
VM - Creates a full operating system stack, uses more system resources regardless of applications used
Container - Shares the operating system plateform (can choose not to) and allows more granualar management of resources based on uses.   _(e.g. user can contain a working web app like nginx to be used on top of a variety of Linux distrobution / systems)_

## Docker Architecture

#### Container architecture
   - Is a client-server application
   - Run as a [Same System] Docker client + Docker daemon
   - Or a [Remote System] Docker client > Remote Docker daemon
   - Runs on top of a single Linux instance
   - Docker engine = incapsulating proccess
   - Removes a whole layer from the VM hypervisor process
      + saves resources and space
    
#### Docker client & daemon communications
   - Sockets
   - RESTful API (Representational State Tranfer - Stateless transfer over HTTP of a webpage containing an XML file)
    
#### Docker Components
   - Daemon
   - Client
   - Docker.io Registry
    
#### Docker is not a new idea
Docker has captured the right buzz
Other Applications Include:
   - FreeBSD - Jails
   - Sun (Oracle) Solaris - Zones
   - Google - lmcfy (Let Me Contain That For You)
   - OpenVZ
     
## The Docker Hub
   - Public registry/repository
   - Maintained by Docker Inc.
   - Conatains images to build containers
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

**Lets create a repository for Docker!**

Move to repo directory
```
cd /etc/yum.repos.d/
```
View repo's
```
ll
```
Create repo file
```
sudo vim docker.repo
```
Type the following in the repo file
```
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/$releaserver/
enabled=1
gpgcheck=1
gpg=https://yum.dockerproject.org/gpg
```        
Update the cache and latest updates
```
sudo yum -y update
```        
Install docker from the docker repo
```
sudo yum -y install docker-engine
```
Enable & Start Docker
```
sudo systemctl enable docker
sudo systemctl start docker
```
Verfiy Docker
```
sudo systemctl status docker
```
Verify Docker images (you should get "Cannot connect to the Docker deamon.")
```
docker images
```
Why does this happen?
By default docker installs docker.sock under the "docker" group
To see what we are talking about:
```
cd /var/run
ls -al dock*
```
_Only Root or docker group can access Docker because of docker.sock permissions_
**The Fix**
Add our user to the docker group
```
sudo usermod "user" -G docker
```
Logout & Login to inact user modification
```
exit
su "user"
```
Reinsure docker is working properly
```
docker run hello-world
```
Verify latest docker version
```
docker --version
```
## Creating Our First Image
