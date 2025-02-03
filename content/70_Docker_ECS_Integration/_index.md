+++
title = "Module 2"
chapter = true
weight = 22
+++

# Deploy an Application to AWS Using the Docker Amazon ECS Plugin

## Learning Objectives
Now that the application is up and running locally, lets now see how to seamlessly and without much changes migrate the same application on to AWS without modifying the developer experience and using the same `docker compose` commands. 
Before we move further, let's talk about some of the steps needed in order to utilize the ECS plugin for Docker Compose. 

## What is a Docker Context?
A single Docker CLI can have multiple **contexts**. Each context contains all of the endpoint and security information required to manage a different cluster or node. The docker context command makes it easy to configure these contexts and switch between them. As an example, a single Docker client on your company laptop might be configured with two contexts; dev-k8s and prod-swarm. dev-k8s contains the endpoint data and security credentials to configure and manage a Kubernetes cluster in a development environment. prod-swarm contains everything required to manage a Swarm cluster in a production environment. Once these contexts are configured, you can use the top-level docker context use <context-name> to easily switch between them.

The other reason why contexts are important to understand is that you need to create a context for Amazon ECS specifically in order to be able to deploy your application to ECS as it includes the specific configurations and endpoints to do so. 

#### Create a Docker Context for Amazon ECS

A context is a combination of several properties. These include:

* Name
* Endpoint configuration
* TLS info
* Orchestrator

Let's first take a look at what contexts we have on our local machine. Run the follow command to view a list of contexts.

```
$ docker context ls
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT               KUBERNETES ENDPOINT                                 ORCHESTRATOR
default *           moby                Current DOCKER_HOST based configuration   unix:///var/run/docker.sock   https://kubernetes.docker.internal:6443 (default)   swarm
```

The `docker context ls` command prints out a list of contexts currently configured on your local machine. You'll notice the `default` context has a `*` beside it. This denotes that it is the current context that all Docker commands will use.
  
Now lets create a context that we can use to target ECS. To do this, we'll use the `docker context create` command. 

```
$ docker context create ecs --help
Create a context for Amazon ECS

Usage:
  docker context create ecs CONTEXT [flags]

Flags:
      --access-keys string   Use AWS access keys from file
      --description string   Description of the context
      --from-env             Use AWS environment variables for profile, or credentials and region
  -h, --help                 Help for ecs
      --local-simulation     Create context for ECS local simulation endpoints
      --profile string       Use an existing AWS profile

Global Flags:
      --config DIRECTORY   Location of the client config files DIRECTORY (default "/Users/anshrma/.docker")
  -c, --context string     context
  -D, --debug              Enable debug output in the logs
  -H, --host string        Daemon socket(s) to connect to

```

As you can see, the `docker context create ecs` command takes a `CONTEXT` as parameter. The `CONTEXT` parameter will be used to name the context. Let's run the basic command without passing any flags.

```
$ docker context create ecs ecs-workshop
? Create a Docker context using:  [Use arrows to move, type to filter]
   AWS secret and token credentials
>  AWS environment variables

```

The command recognizes that we did not provide a profile and will ask us to select one to use or create a new one. Let's create a new one. Move the arrow (`>`) so that it is pointing to the `AWS environment variables` option and press enter.

Lets change the context to use AWS ECS by using `docker context use ecs-workshop`.


Let's print out the list of contexts.
```
$ docker context ls
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT               KUBERNETES ENDPOINT                                 ORCHESTRATOR
default             moby                Current DOCKER_HOST based configuration   unix:///var/run/docker.sock   https://kubernetes.docker.internal:6443 (default)   swarm
                                                                                         
ecs-workshop *      ecs                 (us-east-1)            
```

We now have a docker context pointing to the AWS ECS service in us-east-1. So far, we did not deploy anything yet to the ECS.


## Create Secrets in AWS Secrets Manager using docker command

### What is AWS Secrets Manager?

AWS Secrets Manager Secrets Manager enables you to replace hardcoded credentials in your code, including passwords, with an API call to Secrets Manager to retrieve the secret programmatically. This helps ensure the secret can't be compromised by someone examining your code, because the secret no longer exists in the code. Also, you can configure Secrets Manager to automatically rotate the secret for you according to a specified schedule. This enables you to replace long-term secrets with short-term ones, significantly reducing the risk of compromise. This includes API keys and OAuth tokens which we will be using later in this workshop. 

### Create a secret in AWS Secrets Manager using your Docker Hub credentials

Create a file named `docker-pull-creds.json` and add the following to it. Amazon ECS will use this token to retrieve the images from Docker Hub.

`touch docker-pull-creds.json`

```
{
   "username":"YOUR DOCKERHUB USERID",
   "password":"YOUR DOCKERHUB TOKEN or PASSWORD"
}

```

Run following to create a secret in AWS Secrets Manager and save the ARN (Amazon Resource Number) to an environment variable

```
DOCKER_PULL_SECRETS_MANAGER=$(docker secret create pullcredentials docker-pull-creds.json)
echo $DOCKER_PULL_SECRETS_MANAGER
```

In the next section, we will deploy our application to Amazon ECS. 


