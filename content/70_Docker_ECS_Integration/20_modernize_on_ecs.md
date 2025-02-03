+++
title = "Step 2: Modernize on ECS"
chapter = false
weight = 21
+++

# Modernize the sample application to AWS ECS using Docker Compose

## Learning Objectives

Now that we have migrated the application to AWS and succesfully running it on AWS, lets take a step forward and start modernizing it, again without modifying the developer experience , using the same docker compose command line interface. For a complete list and Docker Compose to ECS mapping, refer [here](https://docs.docker.com/cloud/ecs-compose-features/) and [examples](https://docs.docker.com/cloud/ecs-compose-examples/)

In this step, we will do following

* Enable AutoScaling for the AWS Fargate tasks
* Use MQSQL database hosted on AWS Relational Database System (RDS), instead of the container.

## What is AWS Fargate?
AWS Fargate is a serverless compute engine for containers that works with both Amazon Elastic Container Service (ECS) and Amazon Elastic Kubernetes Service (EKS). Fargate makes it easy for you to focus on building your applications. Fargate removes the need to provision and manage servers, lets you specify and pay for resources per application, and improves security through application isolation by design.


## Enable Autoscaling for AWS Fargate tasks

Inspect `docker-compose.prod.scaling.yaml`. This compose file, once overlayed with the remaining two, will do the following

* Increase the static replica count to 2 for the backend service, as specified using services.backend.deploy.replicas . This by itself does not makes the service autoscaling
* Use x-aws-autoscaling custom extension to define the auto-scaling range, as well as cpu or memory to define target metric, expressed as resource usage percent. This will scale the service by increasing the task count by 1, whenever the CPU threshold hits 75% for 5 minutes.

```
services:
  backend:
    cap_add: 
      - SYS_PTRACE
    deploy:
      replicas: 2
      # resources:
      #   limits:
      #     memory: 2Gb
      #     cpus: '0.5'
      x-aws-autoscaling:
          min: 2
          max: 10 #required
          cpu: 75
```

## Use MQSQL database hosted on AWS Relational Database System (RDS), instead of the container

Inspect `x-aws-cloudformation` under `docker-compose.prod.scaling.yaml`. `x-aws-cloudformation` is a custom extension for docker compose ,which allows creation of AWS resources using AWS CloudFormation. In our case, we are using this extension to create following resources

x-aws-cloudformation property | Purpose 
--- | --- 
DBSecurityGroup | This is the security group, defining the ingress rule/CIDR blocks. We are specifying default [VPC CIDR blocks](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html)
RDSInstanceSecret | Generates username and passwords to connect to RDS database , stored securely in AWS Secrets Manager
MySQL | Creates RDS MySQL 8.0.16 database 

## Start the updates

Run the following commands, which will append and modify the compose properties and creates/updates the AWS Resources

```
DOCKER_HUB_ID=${DOCKER_HUB_ID} DOCKER_PULL_SECRETS_MANAGER=${DOCKER_PULL_SECRETS_MANAGER} docker compose -f docker-compose.yaml -f  docker-compose.prod.migrate.yaml -f docker-compose.prod.scaling.yaml up
```

This will take around 10-15 minutes to complete. You can see the progress at https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks?filteringStatus=active&filteringText=&viewNested=true&hideStacks=false

```
DOCKER_HUB_ID=${DOCKER_HUB_ID} DOCKER_PULL_SECRETS_MANAGER=${DOCKER_PULL_SECRETS_MANAGER} docker compose -f docker-compose.yaml -f  docker-compose.prod.migrate.yaml -f docker-compose.prod.scaling.yaml up
WARNING services.build: unsupported attribute        
WARNING services.restart: unsupported attribute      
WARNING services.restart: unsupported attribute      
WARNING services.build: unsupported attribute        
WARNING services.restart: unsupported attribute      
[+] Running 0/0
 ⠋ docker-compose-ecs-sample                      UpdateInProgress User Initiated                               0.0s
 ⠋ Cluster CreateComplete         0.0s
 ⠋ BacknetNetwork                                 CreateComplete         0.0s
 ⠋ LogGroup                                       CreateComplete         0.0s
 ⠋ DbdataAccessPoint                              CreateComplete         0.0s
 ⠋ FrontendTCP80TargetGroup                       CreateComplete         0.0s
 ⠋ CloudMap                                       CreateComplete         0.0s
 ⠋ DockercomposeecssampledbpasswordSecret         CreateComplete         0.0s
 ⠋ FrontendTaskExecutionRole                      CreateComplete         0.0s
 ⠋ FrontnetNetwork                                CreateComplete         0.0s
 ⠋ BackendTaskExecutionRole                       CreateComplete         0.0s
 ⠋ DbTaskExecutionRole                            CreateComplete         0.0s
 ⠋ FrontnetNetworkIngress                         CreateComplete         0.0s
 ⠋ Frontnet80Ingress                              CreateComplete         0.0s
 ⠋ DbdataNFSMountTargetOnSubnet00446a0ba3673b321  CreateComplete         0.0s
 ⠋ DbdataNFSMountTargetOnSubnet0bac6bf40d80b8767  CreateComplete         0.0s
 ⠋ DbdataNFSMountTargetOnSubnet07535421cede4e78c  CreateComplete         0.0s
 ⠋ DbdataNFSMountTargetOnSubnet098a392fcf02dca72  CreateComplete         0.0s
 ⠋ DbdataNFSMountTargetOnSubnet0d065a94a70483f75  CreateComplete         0.0s
 ⠋ LoadBalancer                                   CreateComplete         0.0s
 ⠋ DbdataNFSMountTargetOnSubnet0d91e116d3515d9df  CreateComplete         0.0s
 ⠋ BacknetNetworkIngress                          CreateComplete         0.0s
 ⠋ DbTaskRole                                     CreateComplete         0.0s
 ⠋ FrontendTaskDefinition                         CreateComplete         0.0s
 ⠋ BackendTaskDefinition                          CreateComplete         0.0s
 ⠋ DbTaskDefinition                               CreateComplete         0.0s
 ⠋ BackendServiceDiscoveryEntry                   CreateComplete         0.0s
 ⠋ DbServiceDiscoveryEntry                        CreateComplete         0.0s
 ⠋ FrontendServiceDiscoveryEntry                  CreateComplete         0.0s
 ⠋ FrontendTCP80Listener                          CreateComplete         0.0s
 ⠋ DbService                                      CreateComplete         0.0s
 ⠋ BackendService                                 CreateComplete         0.0s
 ⠋ FrontendService                                CreateComplete       
 ```

* Once complete, go to https://console.aws.amazon.com/rds/home?region=us-east-1#databases: to view the RDS database created. Note the RDS Endpoint and Port.
* Retrieve the credentials to connect to the database by going to the Secrets Manager at https://console.aws.amazon.com/secretsmanager/home?region=us-east-1#!/secret?name=RDSInstanceSecret

For the purpose of this workshop, we will query AWS CloudFormation and set the RDS database endpoint and Secrets Manager ARN to the environment variables so that the compose files can pick it up

```
RDS_ENDPOINT=$(aws cloudformation describe-stacks --stack-name docker-compose-ecs-sample --query Stacks[0].Outputs[0].OutputValue | tr -d \")
RDS_DB_SECRET_ARN=$(aws cloudformation describe-stack-resource --stack-name docker-compose-ecs-sample --logical-resource-id RDSInstanceSecret --query StackResourceDetail.PhysicalResourceId | tr -d \")
```

Now lets bind these resources to the backend application by running following

```
DOCKER_HUB_ID=${DOCKER_HUB_ID} DOCKER_PULL_SECRETS_MANAGER=${DOCKER_PULL_SECRETS_MANAGER} RDS_ENDPOINT=${RDS_ENDPOINT} RDS_DB_SECRET_ARN=${RDS_DB_SECRET_ARN} docker compose -f docker-compose.yaml -f  docker-compose.prod.migrate.yaml -f docker-compose.prod.scaling.yaml -f docker-compose.prod.rds.yaml up
```

What did we just do ? `cat docker-compose.prod.rds.yaml` to inspect the file

* We set RDS database endpoint in environment variable and passed it on to the compose file
* We set the AWS Secrets Manager Identifier (and not the actual secret) in environment variable and passed it on to the compose file
* docker compose automatically passed on [secret](https://docs.docker.com/cloud/ecs-compose-examples/#secrets) specified in  `docker-compose.prod.rds.yaml` under secrets.db-password.name to be mounted on to the backend application, so that the application connects to the RDS database instead of the container


You can run the same application endpoints, but the requests will start going to the RDS instance instead.


## Cleanup

Run `docker compose down` to tear down the ECS environment by deleting the cloudformation templates

```sh
Admin:~/environment/docker-compose-ecs-sample (main) $ docker compose down
[+] Running 11/13
 ⠸ docker-compose-ecs-sample      DeleteInProgress User Initiated                                                                                                                                                          246.4s
 ⠿ FrontendService                DeleteComplete          228.0s
 ⠿ DefaultNetworkIngress          DeleteComplete            1.0s
 ⠿ Frontnet3000Ingress            DeleteComplete            1.0s
 ⠿ BacknetNetworkIngress          DeleteComplete            1.0s
 ⠿ FrontnetNetworkIngress         DeleteComplete            1.0s
 ⠿ FrontendTCP3000Listener        DeleteComplete            1.0s
 ⠿ FrontendTaskDefinition         DeleteComplete            2.0s
 ⠸ BackendService                 DeleteInProgress         15.4s
 ⠿ FrontendServiceDiscoveryEntry  DeleteComplete            1.0s
 ⠿ FrontendTCP3000TargetGroup     DeleteComplete            1.0s
 ⠿ LoadBalancer                   DeleteComplete            1.0s
 ⠿ FrontendTaskExecutionRole      DeleteComplete            2.0s
 ```