+++
title = "Module 2"
chapter = true
weight = 22
+++

# Deploy an Application to AWS Using the Docker Amazon ECS Plugin

## Learning Objectives
Now that the application is up and running locally, lets now see how to seamlessly and without much changes migrate the same application on to AWS without modifying the developer experience and using the same `docker compose` commands. 
Before we move further, let us do some preparation

## Preparation

#### Create and Docker Context for Amazon ECS

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
  An existing AWS profile
> AWS secret and token credentials
  AWS environment variables

```

The command recognizes that we did not provide a profile and will ask us to select one to use or create a new one. Let's create a new one. Move the arrow (`>`) so that it is pointing to the `AWS secret and token credentials` option and press enter.


Now we are asked if we want to provide AWS credentials. Enter `us-east-1` for the region. 

```
$ docker context create ecs ecs-workshop
? Create a Docker context using: AWS secret and token credentials
Retrieve or create AWS Access Key and Secret on https://console.aws.amazon.com/iam/home?#security_credential
? AWS Access Key ID YOUR_ACCESS_KEY
? Enter AWS Secret Access Key ****************************************
? Region us-east-1
Successfully created ecs context "ecs-workshop"

```

Lets change the context to use AWS ECS by using `docker context use ecs-workshop`.


Let's print out the list of contexts.
```
$ docker context ls
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT               KUBERNETES ENDPOINT                                 ORCHESTRATOR
default             moby                Current DOCKER_HOST based configuration   unix:///var/run/docker.sock   https://kubernetes.docker.internal:6443 (default)   swarm
                                                                                         
ecs-workshop *      ecs                 (us-east-1)            
```

We now have a docker context pointing to the AWS ECS service in us-east-1. So far, we did not deploy anything yet to the ECS.


#### Create Secrets in AWS Secrets Manager using docker command

Create a file named `docker-pull-creds.json` and add the following to it. Amazon ECS will use this token to retrieve the images from docker hub.

`touch docker-pull-creds.json`

```
{
   "username":"YOUR DOCKERHUB USERID",
   "token":"YOUR DOCKERHUB TOKEN"
}

```

Run following to create a secret in AWS Secrets Manager and save the ARN (Amazon Resource Number) to an environment variable

```
DOCKER_PULL_SECRETS_MANAGER=$(docker secret create pullcredentials /docker-compose-ecs-sample/docker-pull-creds.json)
echo $DOCKER_PULL_SECRETS_MANAGER
```

Now that we are done with preparation steps, lets now deploy the application to AWS ECS.


