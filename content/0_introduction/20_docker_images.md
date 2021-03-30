+++
title = "Docker Images"
chapter = false
weight = 20
+++

## What is a container image?
A container image is a read-only template with instructions for creating a container. The image contains the code that will run including any definitions for any libraries and dependencies that your code needs to run. Often, an image is based on another image with some additional customization. It is important to note that these images are not just one monolithic block, but are composed of **many layers**. We'll dive into what this means in this section. 

## Dockerfiles and Docker images
Now that we understand the general concepts around what a container image is, let's dive into how this applies to Docker images that can also be known as a Dockerfile. Just like the container image we mentioned above, Dockerfiles and Docker images also provide a portable runtime application environment, package applications and dependencies in a single, immutable artifact, gives users the option to run different application versions simultaneously, and gives faster development and deployment cycles. 

## Layered Architecture of Docker Container Images 
Docker container images follow what is known as a layered architecture. Below is an example of a basic Dockerfile and to demonstrate let's break down what a layered architecture actually means. A Docker image is built up from a series of layers. Each layer represents an instruction in the image’s Dockerfile. Each layer except the very last one is read-only. Consider the following Dockerfile:

```
FROM ubuntu:15.04
COPY . /app
RUN make /app
CMD python /app/app.py
```
This Dockerfile contains four commands, each of which creates a layer. The FROM statement starts out by creating a layer from the ubuntu:15.04 image. The COPY command adds some files from your Docker client’s current directory. The RUN command builds your application using the make command. Finally, the last layer specifies what command to run within the container.

Each layer is only a set of differences from the layer before it. The layers are stacked on top of each other. When you create a new container, you add a new writable layer on top of the underlying layers. This layer is often called the “container layer”. All changes made to the running container, such as writing new files, modifying existing files, and deleting files, are written to this thin writable container layer. The diagram below shows a container based on the Ubuntu 15.04 image.

![Docker](/images/docker-layers.jpeg)

What would happen if you were to change something in the Dockerfile and rebuilt the image? One of the major benefits of using Dockerfiles to build you container images is that only the **modified layers** get rebuilt. This is what makes container images lightweight, small, and fast compared to other virtualization technologies. 

### Summary
Dockerfiles are the source of truth when it comes to building and running Docker images. The Dockerfile is an artifact that you create which in turn produces a Docker image when you build it. The Dockerfile includes instruction on exactly how to build that image and what that image should include. A great way to think about Dockerfiles is that it is no different than following direction to cook your favorite meal. Dockerfiles include step by step instruction on how your container image should be built. Examples could be to pull a specific version of Node.js or may include instructions to install programs like [jq](https://stedolan.github.io/jq/) so that when you run your container, you have everything you need and removes the need to manually install any tools or dependencies you would need to run the container properly. 

Once you have built your Docker image from the instructions laid out in the Dockerfile, the next step is to push that image into an image repository like Docker Hub (we'll discuss what this means in the next section) and then you have the choice of running that container locally to see that it work or deploy that image to Amazon ECS (we will also discuss that this means later)

In this section we covered what a Dockerfile is and how it is used to create container images. In the next section, we will cover where we can store these images also known as Docker Hub. 