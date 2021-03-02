+++
title = "Module 1: Build a Container Image with Docker Buildkit and Deploy to Amazon ECS"
chapter = true
weight = 21
+++

# Building images with Buildkit and deploying to Amazon ECS

In this module we will look at building a simple Docker Compose file and using that file to deploy a simple webserver using NGINX. We will be building our images with Docker BuildKit which will then be pushed to Docker Hub as our image repository of choice for this module. The final step will be to deploy our image to Amazon ECS using the same Docker Compose file. 
We'll also take a look at scaling the service and reading logs all using our compose file and `docker compose` commands.

Before we do that, let's take a look at what a container image is specifically and how images are used in order to build our applications to help give us a better understanding of what a Dockerfile, Compose file, and other various methods for building container images. 

## What is a container image?
A container image is a read-only template with instructions for creating a container. The image contains the code that will run including any definitions for any libraries and dependencies that your code needs to run. Often, an image is based on another image with some additional customization. It is important to note that these images are not just one monolithic block, but are composed of **many layers**.

## Dockerfiles and Docker images
Now that we understand the general concepts around what a container image is, let's dive into how this applies to Docker images that can also be known as a Dockerfile. Just like the container image we mentioned above, Dockerfiles and Docker images also provide a portable runtime application environment, package applications and dependencies in a single, immutable artifact, gives users the option to run different application versions simultaneously, and gives faster development and deployment cycles. 

## Layered Architecture of Docker Container Images 
Docker container images follow what is known as a layered architecture. Below is an example of a basic Dockerfile and to demonstrate let's break down what a layered architecture actually means. A Docker image is built up from a series of layers. Each layer represents an instruction in the image’s Dockerfile. Each layer except the very last one is read-only. Consider the following Dockerfile:

```
FROM ubuntu:18.04
COPY . /app
RUN make /app
CMD python /app/app.py
```
This Dockerfile contains four commands, each of which creates a layer. The FROM statement starts out by creating a layer from the ubuntu:18.04 image. The COPY command adds some files from your Docker client’s current directory. The RUN command builds your application using the make command. Finally, the last layer specifies what command to run within the container.

Each layer is only a set of differences from the layer before it. The layers are stacked on top of each other. When you create a new container, you add a new writable layer on top of the underlying layers. This layer is often called the “container layer”. All changes made to the running container, such as writing new files, modifying existing files, and deleting files, are written to this thin writable container layer. The diagram below shows a container based on the Ubuntu 15.04 image.

![Docker](/images/container-layers.jpg)

What would happen if you were to change something in the Dockerfile and rebuilt the image? One of the major benefits of using Dockerfiles to build you container images is that only the **modified layers** get rebuilt. This is what makes container images lightweight, small, and fast compared to other virtualization technologies. 

## Container Registries 
In the sections above, we have defined what a container image is, how to build containers from those images, and how Dockerfiles make creating container images easier for users. Examples of container registries are DockerHub, Amazon ECR, and GitHub Images. In order for users to be able to share and store images, container registries are vital for being able to share images between teams 


























## Sample application overview

The application that we will use for this module is made up of a React.js frontend and a Node.js backend. We will also take a look at persiting data using mongodb and volumes.

### Clone application sample application

The source code for our sample application is located on GitHub. Login to your GitHub account, and fork the repo: https://github.com/pmckeetx/aws-docker-workshop. Once you have forked the repo, clone it to your local machine.

> **NOTE**: _Replace **`[github-user-account]`** with your GitHub account in the command below._
```
$ git clone git@github.com:[github-user-account]/aws-docker-workshop.git
```

## Build images and run locally

Now that we have our sample application locally, let's build the images and then push those to Docker Hub. 

### Compose

In your terminal, navigate to the root directory of the sample application and open the `docker-compose.yml` file in your favorite text editor.

```
version: '3.8'

services:
  frontend:
    image: [docker-id]/crud-frontend:1.0.0
    build: 
      context: ./frontend
    ports:
      - "80:80"
    networks: 
      - frontend
      - backend
  
  backend:
    image: [docker-id]/crud-backend:1.0.0
    build:
      context: ./backend
    networks: 
      - backend

networks: 
  frontend:
  backend:
```

Let's take a minute and walk through the compose file. As you can see above, we have two services: `frontend` and `backend`. The `frontend` services is our React.js app and is located in the `./frontend` directory. The `backend` services is our Node.js REST api and is located in the `./backend` directory. We tell Docker what to name our images by setting the `image` property and what ports to expose using the `ports` property. If you want to learn more about compose files and the compose spec, please read our [documentation](https://docs.docker.com/compose/) and the [compose spec](https://compose-spec.io/).

Also have a look at the Dockerfile for each project. You'll find then in the root of `./frontend` and `./backend`. 

### Building images using BuildKit

Now let's build our images. We'll use BuildKit and `docker-compose`. BuildKit is a [Moby project](https://docs.docker.com/develop/develop-images/build_enhancements/) that is included in the Docker distrubution and brings improved caching, parralel builds and more. For local builds, `docker-compose` is a convienent way to build images without having to type out long `docker build` commands in the terminal.

ECS does not have the concept of building images. So we need to make sure we are using the `default` context which points to your local Docker Engine.

```
$ docker context use default
```

> To view view which `context` docker is currently using, you can run the `docker context show` command.

Let's turn on BuildKit as our builder. To do that we need to set a couple of environment variables. First well tell the Docker Engine to use BuildKit as it's builder.

```
export DOCKER_BUILDKIT=1
```

Now that Docker Engine is using BuildKit, let's tell Docker compose to use BuildKit also.

```
export COMPOSE_DOCKER_CLI_BUILD=1
```

Now to build our compose application run the following command.

```
$ docker-compose build
Building frontend
[+] Building 7.4s (16/16) FINISHED
...
Successfully built 369a87cf2d6ce440e7021fcb2814319b5d413dca84d3752d91725ec94b2edbdd
Building backend
[+] Building 1.0s (11/11) FINISHED
...
Successfully built a35afae78437118b9ae74165cabda6cec0ad2cf4135d4df9ce0658a279afcd8b
```

To view our newly built images, run the following command.

```
$ 
docker images
REPOSITORY             TAG       IMAGE ID       CREATED         SIZE
[docker-id]/crud-frontend   1.0.0     369a87cf2d6c   3 minutes ago   134MB
[your-docker-id]/crud-backend    1.0.0     a35afae78437   31 hours ago    950MB
```

### Run the application locally

At this point, we've forked and clone the sample application. We took a look at the `docker-compose.yml` to see how our application is configured. Now let's run the application locally using `docker-compose`.

```
$ docker-compose up
WARNING: Native build is an experimental feature and could change at any time
Creating network "aws-ecs-cli_frontend" with the default driver
Creating network "aws-ecs-cli_backend" with the default driver
Creating aws-ecs-cli_backend_1  ... done
Creating aws-ecs-cli_frontend_1 ... done
Attaching to aws-ecs-cli_backend_1, aws-ecs-cli_frontend_1
frontend_1  | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
...
frontend_1  | /docker-entrypoint.sh: Configuration complete; ready for start up
backend_1   | Listening on port 8080 
```

Open your favorite browser and navigate to http://localhost. You'll see the following screen:

![app](./assets/mod_02_crud_basic.png)

Let's make sure our application is running properly.

1. Enter "widgets" in the entity text field.
2. Click the "Create New Record" button.
3. Enter the following json in the text area.
  ```
  {
    "name": "Widget 1"
  }
  ```
4. Click the "Save" button.
5. Scroll to the bottom of the page and view the results in the table.

![results](./assets/mod_02_crud_basic_result.png)

BOOM!

## Deploy to ECS

Now that we have our application images built and running locally, let's take a look at deploying to ECS. To do that, we first need to login to Hub and then push our images so ECS can pull them when we start our cluster.

Login to Docker Hub:
```
$ docker login -u [docker-id]
Password:
Login Succeeded
```

Push images:
```
$ docker-compose push
Pushing frontend (pmckee/crud-frontend:1.0.0)...
The push refers to repository [docker.io/pmckee/crud-frontend]
8553117c6d52: Pushed
...
1.0.0: digest: sha256:74e6dbb7cf532e80671a563b09fdf66e21f45e8a8cb59bd09fb7f92fd7638e8a size: 1779
Pushing backend (pmckee/crud-backend:1.0.0)...
The push refers to repository [docker.io/pmckee/crud-backend]
24895a18ac22: Pushed
...
1.0.0: digest: sha256:ff5e43009b57e721b099e1c803feefd7aae1a5978d14c8b04c3d392023e1d328 size: 3260
```

Compose will read the `docker-compose.yml` file to find the names of the images you would like to push and then push those to Hub.
