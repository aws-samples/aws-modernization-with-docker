+++
title = "Step 1: Migrate the application to AWS ECS"
chapter = false
weight = 21
+++

# Deploy the sample application to AWS ECS using docker compose

## Learning Objectives

In this module, we will take the application that we built in Module 1 and deploy to Amazon ECS. We will use the same `docker compose` commands (without modifying the developer experience). We will overlay the default `docker-compose.yaml` with ` docker-compose.prod.migrate.yaml`. With compose file overlays, we modify only what is required to be modified for a specific environment deployment.

As example, in Module-2, we built images locally using `docker compose build` commands, however, when we want to deploy in to AWS, we want to use the images published to a container repository and pull images to ECS. 


## Inspect the overlay file

Open ` docker-compose.prod.migrate.yaml` to inspect the contents

```
cat  docker-compose.prod.migrate.yaml 
```

Notice the following

Compose Property | Purpose 
--- | --- 
services.frontend.image | Image that we pushed to dockerhub. Note that compose file picks up the environment variables that we set in previous modules.
services.frontend.x-aws-pull_credentials | Credentials to pull images from dockerhub, securely stored in [AWS Secrets Manager](https://aws.amazon.com/secrets-manager/) that we pushed in the preparation


## Deploy to Amazon ECS

Use the same `docker compose up` command to deploy to Amazon ECS

```
DOCKER_HUB_ID=${DOCKER_HUB_ID} DOCKER_PULL_SECRETS_MANAGER=${DOCKER_PULL_SECRETS_MANAGER} docker compose -f docker-compose.yaml -f  docker-compose.prod.migrate.yaml up
```

Notice that we are overlaying services.frontend.image property , mentioned in docker.compose.yaml with the one mentioned in  docker-compose.prod.migrate.yaml.

{{% notice info %}}
We also supplied the environment variables `DOCKER_HUB_ID` and `DOCKER_PULL_SECRETS_MANAGER` , so that the compose file can use it. If you dont mention these values, you will get `invalid reference format` error. 
{{% /notice %}}



You can observe the AWS resources that are getting created. In around 10 minutes, all resources would get created successfully. 

```
$ DOCKER_HUB_ID=${DOCKER_HUB_ID} DOCKER_PULL_SECRETS_MANAGER=${DOCKER_PULL_SECRETS_MANAGER} docker compose -f docker-compose.yaml -f  docker-compose.prod.migrate.yaml up
WARNING services.build: unsupported attribute        
WARNING services.restart: unsupported attribute      
WARNING services.restart: unsupported attribute      
WARNING services.build: unsupported attribute        
WARNING services.restart: unsupported attribute      
[+] Running 34/34
 ⠿ docker-compose-ecs-sample                      CreateComplete 365.7s
 ⠿ Cluster                                        CreateComplete   7.0s
 ⠿ FrontendTCP80TargetGroup                       CreateComplete   1.0s
 ⠿ LogGroup                                       CreateComplete   3.0s
 ⠿ FrontnetNetwork                                CreateComplete   6.0s
 ⠿ BacknetNetwork                                 CreateComplete   6.0s
 ⠿ DbdataFilesystem                               CreateComplete   7.0s
 ⠿ DockercomposeecssampledbpasswordSecret         CreateComplete   3.0s
 ⠿ CloudMap                                       CreateComplete  48.0s
 ⠿ FrontendTaskExecutionRole                      CreateComplete  14.0s
 ⠿ BackendTaskExecutionRole                       CreateComplete  15.0s
 ⠿ DbTaskExecutionRole                            CreateComplete  14.0s
 ⠿ FrontnetNetworkIngress                         CreateComplete   1.0s
 ⠿ BacknetNetworkIngress                          CreateComplete   1.0s
 ⠿ Frontnet80Ingress                              CreateComplete   1.0s
 ⠿ LoadBalancer                                   CreateComplete  93.0s
 ⠿ DbdataNFSMountTargetOnSubnet098a392fcf02dca72  CreateComplete  93.0s
 ⠿ DbdataNFSMountTargetOnSubnet0d065a94a70483f75  CreateComplete  93.0s
 ⠿ DbdataAccessPoint                              CreateComplete   6.0s
 ⠿ DbdataNFSMountTargetOnSubnet0d91e116d3515d9df  CreateComplete  93.0s
 ⠿ DbdataNFSMountTargetOnSubnet07535421cede4e78c  CreateComplete  93.0s
 ⠿ DbdataNFSMountTargetOnSubnet0bac6bf40d80b8767  CreateComplete  93.0s
 ⠿ DbdataNFSMountTargetOnSubnet00446a0ba3673b321  CreateComplete  93.0s
 ⠿ FrontendTaskDefinition                         CreateComplete   3.0s
 ⠿ DbTaskRole                                     CreateComplete  14.0s
 ⠿ BackendTaskDefinition                          CreateComplete   3.0s
 ⠿ DbTaskDefinition                               CreateComplete   3.0s
 ⠿ FrontendServiceDiscoveryEntry                  CreateComplete   3.0s
 ⠿ BackendServiceDiscoveryEntry                   CreateComplete   3.0s
 ⠿ DbServiceDiscoveryEntry                        CreateComplete   3.0s
 ⠿ FrontendTCP80Listener                          CreateComplete   2.0s
 ⠿ DbService                                      CreateComplete  90.0s
 ⠿ BackendService                                 CreateComplete  67.2s
 ⠿ FrontendService                                CreateComplete                              

```


You will notice that Docker Compose had an opinion about creating AWS resources, which all conforms to the AWS Well Architected Principles.
All the resources were deployed to the Default VPC for this lab. In real life scenario, you can deploy to your own VPC and subnets using `x-aws-vpc` extension (commented in  docker-compose.prod.migrate.yaml).

The Docker Compose CLI first concatenates the compose files passed through and generates an opinionated [AWS CloudFormation](https://aws.amazon.com/cloudformation/) template and deploys it to create the AWS resources defined in our compose file. You can run `docker compose convert` command to view the AWS Cloudformation template that is generated.

```

$ DOCKER_HUB_ID=${DOCKER_HUB_ID} DOCKER_PULL_SECRETS_MANAGER=${DOCKER_PULL_SECRETS_MANAGER} docker compose -f docker-compose.yaml -f  docker-compose.prod.migrate.yaml convert > aws-cloudformation.yaml
WARNING services.build: unsupported attribute        
WARNING services.restart: unsupported attribute      
WARNING services.restart: unsupported attribute      
WARNING services.build: unsupported attribute        
WARNING services.restart: unsupported attribute 

```

```
cat aws-cloudformation.yaml
```

Note that the generated AWS Cloudformation template `aws-cloudformation.yaml` has around 650+ lines of code that we never coded, but the same compose files that we used for local development generated it for us in ECS context, thereby keeping a consistant developer experience.

You can view the entire CloudFormation stack at https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks?filteringStatus=active&filteringText=&viewNested=true&hideStacks=false


![Docker](/images/running-in-cloud.png)

Following are the equivalent AWS Resources generated by compose CLI. 

Local | Generated AWS resource | Purpose | More Information
--- | --- | --- | ---
[docker volume](https://docs.docker.com/storage/volumes/) |  [AWS Elastic File System] (https://aws.amazon.com/efs/) | Stateful storage | https://docs.docker.com/cloud/ecs-integration/#volumes
[docker network] (https://docs.docker.com/network/) | [Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) | Service Isolation | https://docs.docker.com/cloud/ecs-integration/#service-isolation
[docker secret](https://docs.docker.com/engine/reference/commandline/secret/) | [AWS Secrets Manager] (https://aws.amazon.com/secrets-manager/) | Secure Secrets Storage | https://docs.docker.com/cloud/ecs-integration/#secrets
[docker service](https://docs.docker.com/engine/reference/commandline/service/) | [Amazon ECS Services] (https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs_services.html) and [Load Balancers] (https://aws.amazon.com/elasticloadbalancing) | 
[Networking in Compose](https://docs.docker.com/compose/networking/) | [AWS CloudMap] (https://docs.docker.com/cloud/ecs-integration/#service-discovery) | Service Discovery | https://docs.docker.com/cloud/ecs-integration/#service-discovery


To visualize how the application looks like in AWS when compared with local

![Docker](/images/running-in-cloud-mapping.png)

Below is the architecture for the application on aws

![Docker](/images/application-on-aws.png)


Run `docker compose ps` to view the list of relevant services that were created on AWS . Note the load balancer URL for your frontend

```
$ docker compose ps
NAME                                                              SERVICE             STATUS              PORTS
task/docker-compose-ecs-sample/03edab2b86bf4908848dc110783534ca   frontend            Running             docke-LoadB-1CUH2S15TOP3G-9f4a862f40ce307b.elb.us-east-1.amazonaws.com:3000->3000/tcp
task/docker-compose-ecs-sample/b1c2b46cbb7a49cc91accc7dd11d792d   db                  Running             
task/docker-compose-ecs-sample/dd137043522843fe8ba85392f3b1175e   backend             Running  
```

Access the URL mentioned above to access the application. Access the application , similarly how you accessed it locally in Module-1. You can access it from any browser or using curl

* Load the application (replace it from the frontend endpoint url from above step)

```
http://docke-LoadB-1CUH2S15TOP3G-9f4a862f40ce307b.elb.us-east-1.amazonaws.com:3000
```

* Insert records in database (replace it from the frontend endpoint url from above step)

```
http://docke-LoadB-1CUH2S15TOP3G-9f4a862f40ce307b.elb.us-east-1.amazonaws.com:3000/add/2/name2
http://docke-LoadB-1CUH2S15TOP3G-9f4a862f40ce307b.elb.us-east-1.amazonaws.com:3000/add/3/name3
http://docke-LoadB-1CUH2S15TOP3G-9f4a862f40ce307b.elb.us-east-1.amazonaws.com:3000/add/4/name4

```

You can run `docker compose logs` to stream the logs from AWS ECS Service.

```

docker compose logs

Db_Secrets_InitContainer  | 2021-03-15T22:35:36.507325Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.19'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.
Db_Secrets_InitContainer  | 2021-03-15T22:35:36.628015Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: '/var/run/mysqld/mysqlx.sock' bind-address: '::' port: 33060
Backend_Secrets_InitContainer  |  * Serving Flask app "hello.py"
Backend_Secrets_InitContainer  |  * Environment: production
Backend_Secrets_InitContainer  |    WARNING: This is a development server. Do not use it in a production deployment.
Backend_Secrets_InitContainer  |    Use a production WSGI server instead.
Backend_Secrets_InitContainer  |  * Debug mode: off
Backend_Secrets_InitContainer  |  * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
Backend_Secrets_InitContainer  | 172.31.55.66 - - [15/Mar/2021 22:38:18] "GET / HTTP/1.0" 200 -
frontend                       | 172.31.58.77 - - [15/Mar/2021:22:38:18 +0000] "GET / HTTP/1.1" 200 26 "-" "ELB-HealthChecker/2.0" "-"

```

