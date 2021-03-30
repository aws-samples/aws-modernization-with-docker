+++
title = "Docker Engine"
chapter = false
weight = 10
+++


 In the previous section, we set the foundation for understanding what a container is along with understanding some of the key differences between virtual machines and containers. In this section, we will do a deep dive into specifically how Docker simplifies the process of building and deploying containers. 

 One of the key reasons why Docker has been an industry leader in the containers space is that Docker creates simple tooling and a universal packaging approach that bundles up all application dependencies inside a container which is then run on Docker Engine.

### What is the Docker Engine?
The Docker Engine enables containerized applications to run anywhere consistently on any infrastructure, solving dependency issues for developers and operations teams, and eliminating the “it works on my laptop!” problem. The Docker Engine encompasses a full set of features and tools to help users be as efficient as possible when working with containers. The three key features and capabilities that is powered by the Docker Engine can be summarized as follows:

- Powered by **containerd** which is an industry-standard container runtime with an emphasis on simplicity, robustness, and portability

- Integration with **Buildkit** which is Dockers most used feature of the Docker Engine. Docker Buildkit is primarily used to build images from a Dockerfile (we will dive deeper into this in Module 2)

- Simplicity and accessibility of the **Docker CLI** which will be highlighted all throughout this workshop

In terms of bringing immediate business value, the Docker Engine helps businesses of all sizes achieve the following three benefits:

- Accelerates innovation as Docker Engine forms the common foundation underlying the Docker Enterprise platform, allowing developers and operators to turn ideas into reality quickly and securely by providing the ability to develop and ship code faster and more efficiently. 

- Gives developer and operations teams the freedom of choice as the Docker Engine supports any type of application. Legacy applications, cloud-native, monolithic, 12-factor applications and even works with multiple operating systems across all cloud providers. The Docker Engine is also validated to work with Kubernetes CRI. 

- Provides intrisic security as the Docker Engine was built with security in mind. With Docker Content Trust and FIPS 140-2 validation, Docker Engine users can run containerized applications in highly regulated environments. 

### Docker Engine Overview
![Docker](/images/docker-engine.png)

Underneath the hood, Docker Engine is a client-server application with the following major components:

- A server which is a type of long-running program called a daemon process (the dockerd command).

- A REST API which specifies interfaces that programs can use to talk to the daemon and instruct it what to do.

- A command line interface (CLI) client (the docker command).

The CLI uses the Docker REST API to control or interact with the Docker daemon through scripting or direct CLI commands. Many other Docker applications use the underlying API and CLI.

The daemon creates and manages Docker objects, such as images, containers, networks, and volumes.

To learn more about some of the different technologies mentioned in this section we have gone ahead and added links for further education

- https://containerd.io/ - To learn more about containerd
- https://docs.docker.com/engine/ - Documentation to learn more about Docker Engine
- https://docs.docker.com/develop/develop-images/build_enhancements/ - Start up guide for using Docker Buildkit

In the next section we will learn about what a container image is and how this will help us to understand what a Docker image is. 