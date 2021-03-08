+++
title = "Module 1: Build a Container Image with Docker Buildkit"
chapter = true
weight = 21
+++

# Building Container Images with Docker Buildkit and Compose

**Estimated Completion Time: 30 minutes - 1 hour**

### Introduction

In this module we will learn how to build our own container images using the Docker Compose CLI tool, Docker Buildkit, and Docker Hub. We will also learn how to utilize Docker Compose files to help build our application. We will then test our application locally to see that it works. Let's discuss what Docker Compose is all about to help us understand the rest of this module. 

## Docker Compose
![Docker](/images/docker-compose.png)
So far, we have learned that we can use Dockerfiles to build our container images for **single container applications** but what do we do if we have an application that requires **multiple container images** to run properly? This is where Docker Compose comes in. 

Docker Compose is a tool for defining and running multi-container Docker applications. With Docker Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration file. Examples would be adding a database or a caching mechansim for your application. We will learn how to utililze these specific mechanisms in Module 2. 

## How does Docker Compose files differ from Dockerfiles?
Users that are new to containers and Docker can get really confused on when and where to use Dockerfiles and Docker Compose files. While there are situations where you could use one or the other, it really comes down to the complexity of your application you plan on deploying. In modern applications, it is very common to see applications use **both**. What this means is that you can have a Docker Compose file that will rely on a Dockerfile in order to build your application. In this section, we will learn how to utilize both in building our application so if this does not make sense at this moment, it certainly will by the end of this module. 

## Sample Application Overview

The application that we will use for this module is made up of a React.js frontend and a Node.js backend which will all be hosted on a webserver created by NGINX. The source code for our sample application is located on GitHub. Login to your GitHub account, and clone the repo: https://github.com/pmckeetx/aws-docker-workshop.git. and change into directory with all the code we will be using in this module. 

```
$ git clone https://github.com/pmckeetx/aws-docker-workshop.git.
```
```
cd ~/environment/aws-docker-workshop
```

If you were able to follow the directions above your Cloud9 file directory should look like the following:

(insert screenshot)

## Build images and push to Docker Hub

Now that we have successfully cloned the repo with all of the code and files we will need for our application, let's walk through how to use the docker-compose files to build our images. 

(insert Cloud9 image)

In your Cloud9 you will see a `docker-compose.yml` file, and open the `docker-compose.yml` file and let's figure out what this file is doing. 

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

As you can see above, we have two services: `frontend` and `backend`. The `frontend` services is our React.js app and is located in the `./frontend` directory. The `backend` services is our Node.js REST api and is located in the `./backend` directory. We tell Docker what to name our images by setting the `image` property and what ports to expose using the `ports` property. 

Also have a look at the Dockerfile for each project. You'll find then in the root of `./frontend` and `./backend`. 

(insert Cloud9 image)

What you'll notice is that both the frontend and backend directories have their own respective Dockerfiles. Earlier in this module we mentioned that Docker Compose files and Dockerfiles can be used together in order to build your applications. In the next step, we will build our image using the ```docker-compose.yml``` file and we'll see how it makes calls to the Dockerfiles that live inside the frontend and backend directories to build our application. 

## Building images using Docker BuildKit

Now let's build our images. We'll use Docker BuildKit and the `docker-compose` CLI to do so. 

Docker BuildKit is a [Moby project](https://docs.docker.com/develop/develop-images/build_enhancements/) that is included in the Docker distrubution and brings improved caching, parralel builds, and more. For local builds, `docker-compose` is a convienent way to build images without having to type out long `docker build` commands in the terminal.

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

## Run the application locally

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

Open your favorite browser and navigate to http://localhost:80. You'll see the following screen:

![app](/images/docker-module1.png)

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

![results](/images/docker-module1-result.png)

As you can see, we were succesfully able to create a new record in our app which means that our application was built succesfully using Docker Compose and BuildKit. 

### Summary 

In this module we learned how to build and run an application locally using Docker Compose and BuildKit. We also learned the following:

- How to differntiate Dockerfiles and ```docker-compose.yml``` files and that we can actually use them together in order to build and run our applications. 
- How to build and push our container images to Docker Hub using the Docker Compose CLI
- Building a container image using Docker BuildKit

In the next section, we will take everything we learned in this module a step further and deploy an application to Amazon ECS adding additional complexity to our compose files using the Docker Compose CLI features built by both Docker and AWS. 

