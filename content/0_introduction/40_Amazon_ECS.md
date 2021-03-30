+++
title = "Amazon ECS"
chapter = false
weight = 40
+++
![Docker] (/images/docker-ecs-overview.png)

### What is Amazon ECS?

Amazon Elastic Container Service (Amazon ECS) is a highly scalable, fast container management service that makes it easy to run, stop, and manage containers on a cluster. Your containers are defined in a task definition that you use to run individual tasks or tasks within a service. In this context, a service is a configuration that enables you to run and maintain a specified number of tasks simultaneously in a cluster. You can run your tasks and services on a serverless infrastructure that is managed by AWS Fargate. Alternatively, for more control over your infrastructure, you can run your tasks and services on a cluster of Amazon EC2 instances that you manage.

### Why Amazon ECS?

Amazon ECS has a variety of different use cases and integrate well with the rest of the AWS ecosystem. Additionally, because ECS has been a foundational pillar for key Amazon services, it can natively integrate with other services such as Amazon Route 53, Secrets Manager, AWS Identity and Access Management (IAM), and Amazon CloudWatch providing you a familiar experience to deploy and scale your containers. Amazon ECS customer use cases range from deploying web applications all the way to machine learning use cases so if your application can be run inside of a container there is very high chance that you will be able to use Amazon ECS as your container orchestration solution of choice. 

### Why customers love using Amazon ECS
- Customers using Amazon ECS allow users to focus on building applications and not infrastrucutre. Amazon ECS provides consistent environments across users to improve developer velocity so you don't have to worry about different dependencies or tooling for your containers to run. 
- Amazon ECS will scale quickly and seamlessly for you so that you don't have to do it manually. There is a reduced operational burden on you as as a user because AWS has removed the undifferentiated heavy lifting of having to manage the infrastructure as demand changes on your application. 
- Amazon ECS is designed with a security first mindset and is isolated by design. Users that use Amazon ECS can deploy and manage their applications with ease of mind because Amazon ECS has been built with uniform security across environments and is maintained with automation.

### Partner Ecosystem Support for Amazon ECS 

The Amazon Partner Network (APN) has a vast set of partners that support Amazon ECS in different ways that help meet customer requirements and needs from a variety of different use cases. There are four different categories that the APN provides for partners which include the following:

- Foundational
- Monitoring & Logging
- DevOps 
- CI/CD
- Security 

The APN also has a set of consulting partners that can help assist you with the implementation stage of your project.

To learn more about APN Container partners check out [this] (https://aws.amazon.com/containers/partner-solutions/?partner-solutions-cards.sort-by=item.additionalFields.partnerNameLower&partner-solutions-cards.sort-order=asc) link.

In the next section we will walk you through how to set up your environment to be able to properly go through this workshop.  