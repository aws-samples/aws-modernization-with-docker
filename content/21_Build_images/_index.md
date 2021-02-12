+++
title = "Module 2: Build a Container Image with Docker Buildkit and Deploy to Amazon ECS"
chapter = true
weight = 21
+++
# Building images with Buildkit and deploying to ECS

In the first module we took a look at building a simple compose file and using that to deploy a simple webser using NGINX. In this module we'll take a look at building our images with BuildKit, Pushing those Docker Hub and then deploying to ECS using a compose file. We'll also take a look at scaling the service and reading logs all using our compose file and `docker compose` commands.

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
