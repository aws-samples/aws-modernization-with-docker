+++
title = "Module 2: Deploy an Application Using the Docker Amazon ECS Plugin"
chapter = true
weight = 22
+++

# Deploy an Application Using the Docker Amazon ECS Plugin

**Estimated Completion Time: 1 Hour**

### **Introduction**
In this section, we will learn about the Docker Amazon ECS plugin that allows the ability for users to deploy their Docker applications directly to Amazon ECS using the Docker Compose CLI. We will also learn about Docker Contexts and how we can use contexts to manage our applications. You will also notice that the ```docker-compose.yml``` files have additional components that we will be using to deploy our application for this module. We will also dive into what these additional components are and how they can help us build our application using AWS and Docker best practices. 

## Docker Amazon ECS Plugin
The integration between Docker Compose and Amazon ECS allows users to deploy Compose applications to ECS simply using the Docker command line. The Docker Compose ECS integration relies on CloudFormation to manage AWS resources as an atomic operation. What this means is that when you deploy a Compose application to ECS, it will generate a CloudFormation template under the hood. 


This provides an additional benefit for users deploying their application to ECS as you have the ability to take the CloudFormation and modify or add addtional components as needed. 

## Docker Context
Ability to point to container runtime and deploy your apps using the docker commands you already know docker compose up, down, ps

## Create and Manage Contexts
Let's first take a look at what contexts we have on our local machine. Run the follow command to view a list of contexts.

```
$ docker context ls
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT               KUBERNETES ENDPOINT                                 ORCHESTRATOR
default *           moby                Current DOCKER_HOST based configuration   unix:///var/run/docker.sock   https://kubernetes.docker.internal:6443 (default)   swarm
```

The `docker context ls` command prints out a list of contexts currently configured on your local machine. You'll notice the `default` context has a `*` beside it. This denotes that it is the current context that all Docker commands will use.
  
Now lets create a context that we can use to target ECS. To do this, we'll use the `docker context create` command. Let's take a look at the help for this command.
```
$ docker context create --help
Create a new context

Create docker engine context:
$ docker context create CONTEXT [flags]

Create Amazon ECS context:
$ docker context create ecs CONTEXT [flags]
(see docker context create ecs --help)
...
```

I've truncated the output to the most relevant point. That is creating an Amazon ECS context. Let's take a look at the help for the `docker context create ecs` command.

```
$ docker context create ecs --help
Create a context for Amazon ECS

Usage:
  docker context create ecs CONTEXT [flags]

Flags:
      --description string   Description of the context
  -h, --help                 Help for ecs
      --local-simulation     Create context for ECS local simulation endpoints
      --profile string       Profile
      --region string        Region

Global Flags:
      --config DIRECTORY   Location of the client config files DIRECTORY (default "/Users/peter/.docker")
  -c, --context string     context
  -D, --debug              Enable debug output in the logs
  -H, --host string        Daemon socket(s) to connect to
```

As you can see, the `docker context create ecs` command takes a `CONTEXT` as parameter. The `CONTEXT` parameter will be used to name the context. Let's run the basic command without passing any flags.

```
$ docker context create ecs ecs
? Select AWS Profile  [Use arrows to move, type to filter]
> new profile
  default
  workshop
```

The command recognizes that we did not provide a profile and will ask us to select one to use or create a new one. Let's create a new one. Move the arrow (`>`) so that it is pointing to the `new profile` option and press enter.

We'll use the name "workshop". Enter that as your profile name.
```
$ docker context create ecs ecs
? Select AWS Profile new profile
? profile name workshop
```

Now we are asked if we want to provide AWS credentials. If you do not want to provide credentials now, you can add them to the `~/.aws/credentials` later. Let's enter them now.
```
$ docker context create ecs ecs
? Select AWS Profile new profile
? profile name workshop
? Enter AWS credentials yes
? AWS Access Key ID XXXXXXXXXXXXXXXXXXXX
? Enter AWS Secret Access Key ****************************************
```

Enter `us-east-1` for the region. 
```
$ docker context create ecs ecs
? Select AWS Profile new profile
? profile name workshop
? Enter AWS credentials yes
? AWS Access Key ID XXXXXXXXXXXXXXXXXXXX
? Enter AWS Secret Access Key ****************************************
? Region us-east-1
Successfully created ecs context "ecs"
```

Let's print out the list of contexts.
```
$ docker context ls
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT               KUBERNETES ENDPOINT                                 ORCHESTRATOR
default *           moby                Current DOCKER_HOST based configuration   unix:///var/run/docker.sock   https://kubernetes.docker.internal:6443 (default)   swarm
ecs                 ecs                 (us-east-1)
```

BOOOM! There we go. We now have a docker context pointing to the AWS ECS service in us-east-1.

## Deploy single application using ECS context
Now that we have a context pointing to ECS, we'll use it to deploy a simple NGINX webserver. To do that, we'll create a compose file to describe our service. 

Create a text file named `docker-compose-nginx.yml` and add the follow yaml to it.

`$ touch docker-compose.yml`

  ```		
  version: "3.8"

  services:
    webserver:
      image: nginx
      ports:
        - "80:80"
  ```

As you can see at the top of the compose file, we are using version 3.8 of the compose spec. Next we create a section to configure our services. Then we created a service named "webserver" and then under the "webserver" section we idetify the image to use and the ports to expose.

Now let's deploy our NGINX webserver to ECS. First let's make sure we are using the `ecs` context we created above.

```
$ docker context use ecs
ecs
```

To deploy your compose application, we'll use the `docker compose up` command. This will read the docker-compose-nginx.yml file and create and start our containers on ECS. Since we are not using the default file name (docker-compose.yml) for our compose file, we'll pass `docker-compose-nginx.yml` to the `--file` flag so docker knows which compose file to use.

```
$ docker compose up --file docker-compose-nginx.yml
[+] Running 14/14
 ⠿ awsecscli                       CREATE_COMPLETE          250.0s
 ⠿ WebserverTaskExecutionRole      CREATE_COMPLETE          14.0s
 ⠿ LogGroup                        CREATE_COMPLETE          3.0s
 ⠿ WebserverTCP80TargetGroup       CREATE_COMPLETE          1.0s
 ⠿ CloudMap                        CREATE_COMPLETE          48.0s
 ⠿ Cluster                         CREATE_COMPLETE          7.0s
 ⠿ DefaultNetwork                  CREATE_COMPLETE          6.0s
 ⠿ Default80Ingress                CREATE_COMPLETE          1.0s
 ⠿ LoadBalancer                    CREATE_COMPLETE          153.0s
 ⠿ DefaultNetworkIngress           CREATE_COMPLETE          1.0s
 ⠿ WebserverTaskDefinition         CREATE_COMPLETE          3.0s
 ⠿ WebserverServiceDiscoveryEntry  CREATE_COMPLETE          2.0s
 ⠿ WebserverTCP80Listener          CREATE_COMPLETE          1.0s
 ⠿ WebserverService                CREATE_COMPLETE          78.6s
```

Docker will interact with ECS and update progress in the terminal. 

Now that our compose application has been deployed, let's test it out. To find the puplic endpoint for our service, run the following command in your terminal.

```
$ docker compose ps
ID                                        NAME                REPLICAS            PORTS
awsecscli-WebserverService-wZAajRTPqTYU   webserver           1/1                 awsec-LoadB-14UERA5HOC5J2-1947175688.us-east-1.elb.amazonaws.com:80->80/http
```

Copy the uri listed under the `PORTS` column and past into your favorite browser. You will see the following screen.

![NGINX](./assets/mod_01_nginx_basic.png)

BOOM! Our NGINX webserver has been deployed to ECS!

## Tear down the ECS cluster

The Docker ECS integration will handle trearing down all resources that were created when we deployed the application. Later we will take a deeper look at the resources that are created when we do a `docker compose up` but for now let's remove the cluster.

```
$ docker compose down
[+] Running 14/14
 ⠿ awsecscli                       DELETE_COMPLETE          474.0s
 ⠿ DefaultNetworkIngress           DELETE_COMPLETE          2.0s
 ⠿ Default80Ingress                DELETE_COMPLETE          0.0s
 ⠿ WebserverService                DELETE_COMPLETE          396.4s
 ⠿ WebserverServiceDiscoveryEntry  DELETE_COMPLETE          1.6s
 ⠿ WebserverTCP80Listener          DELETE_COMPLETE          0.6s
 ⠿ WebserverTaskDefinition         DELETE_COMPLETE          3.6s
 ⠿ Cluster                         DELETE_COMPLETE          1.6s
 ⠿ LoadBalancer                    DELETE_COMPLETE          1.0s
 ⠿ WebserverTCP80TargetGroup       DELETE_COMPLETE          1.0s
 ⠿ CloudMap                        DELETE_COMPLETE          48.2s
 ⠿ DefaultNetwork                  DELETE_COMPLETE          71.0s
 ⠿ LogGroup                        DELETE_COMPLETE          2.0s
 ⠿ WebserverTaskExecutionRole      DELETE_COMPLETE          2.0s
```

Next we'll take a look at deploying a more complex application and dig into the resources that are created.

### Ported over from Module 1

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
