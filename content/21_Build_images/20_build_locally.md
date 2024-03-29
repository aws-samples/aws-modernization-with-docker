+++
title = "Step 2: Build Image Locally"
chapter = false
weight = 21
+++

## Compose file overview

Now that we have successfully cloned the repo with all of the code and files we will need for our application, let's walk through how to use the docker compose files to build our images locally. 

In your Cloud9 you will see a `docker-compose.yml` file, and open the `docker-compose.yml` file and let's figure out what this file is doing. 

```
services:
  db:
    image: mysql:8.0.19
    command: '--default-authentication-plugin=mysql_native_password'
    restart: always
    secrets:
      - db-password
    volumes:
      - db-data:/var/lib/mysql
    networks:
      - backnet
    environment:
      - MYSQL_DATABASE=example
      - MYSQL_ROOT_PASSWORD_FILE=/run/secrets/db-password
  backend:
    build: 
      context: ./backend
    restart: always
    secrets:
      - db-password
    # ports:
    #   - 5000:5000
    networks:
      - backnet
      - frontnet
    depends_on:
      - db
  proxy:
    build: 
      context: ./proxy
    restart: always
    ports:
      - 3000:80
    networks:
      - frontnet
    depends_on:
      - backend
volumes:
  db-data:
secrets:
  db-password:
    # This is mounted to /run/secrets/ onto the container using it
    file: db/password.txt
networks:
  backnet:
  frontnet:
```

We have three services: `db`, `backend` and `proxy`. Lets go through each and understand the compose specs for each.

### db

This is the [mysql](https://hub.docker.com/_/mysql/) [8.0.19](https://hub.docker.com/layers/mysql/library/mysql/8.0.19/images/sha256-09de7b17af0c17d397e6b69ff841756b80074aed00c1e91d7bc0f3caa5512113?context=explore) version of the image that we intend to use directly from Docker Hub . It mounts a [persistant volume](https://docs.docker.com/storage/volumes/) named as `db-data` and binds it to the `/var/lib/mysql` path.

We use the [secret](https://docs.docker.com/engine/reference/commandline/secret/) file located at `db/password.txt` on your filesystem. The secret is named as `db-password`. This secret is used by the applications `backend` to connect to the mysql database when you run the container images. Secrets are mounted to `/run/secrets` onto the container using it.

We also mount two [environment variables](https://docs.docker.com/compose/environment-variables/) as `MYSQL_DATABASE` which is the database name and `MYSQL_ROOT_PASSWORD_FILE` which is the location of the password file. 

### backend

This service is a python application, the Dockerfile for which is located in `./backend` and uses a secret named as `db-password` that we defined above. We control the order of how the services startup and shutdown using the `depends_on` property which ensures that the `backend` service container will begin initializing only when the `db` is up and running.

Please have a look at the Dockerfile located at `./backend` directory for more context. 

### frontend

This service is just a proxy served up by NGINX to listen for traffic on port 80. The Dockerfile located in the `./frontend` folder will start initializing once the `backend` container is up and running.

The `frontend` services is our Flask app and is located in the `./frontend` directory. The `backend` service is our python REST api and is located in the `./backend` directory. We tell Docker what to name our images by setting the `image` property and what ports to expose using the `ports` property. 

Please have a look at the Dockerfile located at `./frontend` directory for more context. 


## Build image locally using docker compose

We will use `docker compose build` to build the image locally. `docker compose` is written in GoLang. We will use the [default context](https://docs.docker.com/engine/context/working-with-contexts/) to run the application locally.

Change your context to use default

```
$ docker compose build
To provide feedback or request new features please open issues at https://github.com/docker/compose-cli
[+] Building 17.2s (17/17) FINISHED                                                                                                                                                                                                                                             
 => [docker-compose-ecs-sample_backend internal] load build definition from Dockerfile                                                                                                                                               => [docker-compose-ecs-sample_frontend internal] load build definition from Dockerfile                                                                                                                                               
 ```

 Use `docker images` to view the images built

```
$ docker images                                                                                                                                                                                                                  
REPOSITORY                           TAG       IMAGE ID       CREATED         SIZE                                                                                                                                                                                              
docker-compose-ecs-sample_backend    latest    23c47f773f3c   4 minutes ago   67.5MB                                                                                                                                                                                            
docker-compose-ecs-sample_frontend   latest    974b4e988825   4 weeks ago     18MB 

```

## Push images  to Docker Hub

* Set your docker hub id as environment variable

```
DOCKER_HUB_ID=FILL_ME_WITH_YOUR_DOCKER_HUB_ID
```

* Login to Docker Hub (if not done already), using your docker hub id and password that you created during the pre-requisites.


```
$ docker login -u ${DOCKER_HUB_ID}
Password: 
Login Succeeded

```

* Push to Docker Hub

```

docker tag docker-compose-ecs-sample_backend:latest ${DOCKER_HUB_ID}/docker-compose-ecs-sample_backend:latest
docker tag docker-compose-ecs-sample_frontend:latest ${DOCKER_HUB_ID}/docker-compose-ecs-sample_frontend:latest

docker push ${DOCKER_HUB_ID}/docker-compose-ecs-sample_backend:latest
docker push ${DOCKER_HUB_ID}/docker-compose-ecs-sample_frontend:latest

```
