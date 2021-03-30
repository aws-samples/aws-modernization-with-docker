---
title: "Introduction"
weight: 5
chapter: true
draft: false
---
# Workshop Overview

In order to get the most out of your workshop experience, let's set the foundation for understanding why Docker is a leader in the Containerization space. In this section we will discuss the differences between virtual machines and containers to provide more context for this workshop. 

## What is a container and how does using containers make managing and deploying applications more efficient?
A container is a standard unit of software that packages up code and all its dependencies so the applications can run quickly and reliably from one computing environment to another. A container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings. This allows developers to be more productive because instead of having to worry about the various dependencies your colleagues may be using to run their code, they can simply package all their code AND its dependencies into a container so that you have everything you need to stay productive. 


## What is the difference between a virtual machine (VM) and a container?
Containers and virtual machines have similar resource isolation and allocation benefits, but function differently because containers virtualize **the operating system instead of hardware**. Containers are more portable and efficient for this reason and too give us a better understanding of the differences between containers and virtual machines let's look at a comparison of the two. 

### Virtual Machines (VM's)
Virtual machines (VMs) are an abstraction of physical hardware turning one server into many servers. The hypervisor allows multiple VMs to run on a single machine. Each VM includes a full copy of an operating system, the application, necessary binaries and libraries - taking up tens of GBs. VMs can also be slow to boot. The diagram below gives a visual representation of what running a virtual machine would look like. 

![Docker](/images/container-vm-whatcontainer_2.png)

### Containers 
Containers are an abstraction at the app layer that packages code and dependencies together. Multiple containers can run on the same machine and share the OS kernel with other containers, each running as isolated processes in user space. Containers take up less space than VMs (container images are typically tens of MBs in size), can handle more applications and require fewer VMs and Operating systems.

![Docker](images/docker-containerized-appliction-blue-border_2.png)

### Summary
The main take away here is that by **abstracting** away the heavy lifting of application and hardware dependencies in order for you to be effective as a developer you gain the following benefits:

1. Improved efficieny
2. Increased productivity
3. Simplified transactions
4. Reduced costs
5. Increased agility 

The question should also not be whether you should use containers or virtual machines but rather how can you utilize both to provide more flexibility in deploying and managing your applications. The main goal should be to increase agility for your developer teams and realistically speaking, **organizations need to understand how to utilize both in order to achieve these goals.**

In the next section, we will dive into Dockers architecture and talk about the various components that have made Docker the most popular solution for building, managing, and deploying containerized applications.

