# Linux-Acadeny_Docker-Guide
My Notes for Docker, through Linux Academy

## Introduction to Docker

### What is Docker?

Tool or set of tools that packages applications and it dependencies on a virtual container to any Linux systems or distrobutions

### When to use docker?

* Configure Simplification, 
* Developer Productivity, 
* Server Management, 
* Application Isolation,
* Rapid Deployment, 
* [Primary Usage] Build Management Automation (e.g. Ansible, Jenkins)

## Containers Vs. Virtual Machines

VM's - Allows operating systems within another (shares physical machine hardware)
 * VMWare, Citrix, VirtualBox, OpenStack, KVM
 * Usually X86 system 
 * communicates through a Hypervisor
  
Container - An isolated set of packages, libraries, applications

### Whats the difference?

VM - Creates a full operating system stack, uses more system resources regardless of applications used
Container - Shares the operating system plateform (can choose not to) and allows more granualar management of resources based on uses.

## Docker Architecture

  Container architecture
   * Is a client-server application
   * Run as a [Same System] Docker client + Docker daemon
   * Or a [Remote System] Docker client > Remote Docker daemon
    
    Docker client & daemon communications
     * Sockets
     * RESTful API (Representational State Tranfer - Stateless transfer over HTTP of a webpage containing an XML file)
    
  Docker Components
   * Daemon
   * Client
   * Docker.io Registry
    
    
