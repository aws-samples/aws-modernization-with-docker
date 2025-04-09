+++
title = "AWS CI/CD Tools and Services"
chapter = true
weight = 21
+++

# Understanding AWS CI/CD Tools and Services

### What is Git?
Git is an open-source distributed source code management system. Git allows you to create a copy of your repository known as a branch. Using this branch, you can then work on your code independently from the stable version of your codebase. Once you are ready with your changes, you can store them as a set of differences, known as a commit. You can pull in commits from other contributors to your repository, push your commits to others, and merge your commits back into the main version of the repository.

[Learn more about Git..](https://aws.amazon.com/devops/source-control/git/)

---------



### Source control basics
Whether you are writing a simple application on your own or collaborating on a large software development project as part of a team, source control is a vital component of the development process. Source code management systems allow you to track your code change, see a revision history for your code, and revert to previous versions of a project when needed. With source code management systems, you can collaborate on code with your team, isolate your work until it is ready, and quickly trouble-shoot issues by identifying who made changes and what the changes were. Source code management systems help streamline the development process and provide a centralized source for all your code.


Before we begin building our CI/CD pipeline, let's understand the core services and tools we'll be using throughout this workshop. Our pipeline will utilize a suite of AWS services and Docker tools that work together seamlessly to automate the software delivery process. By understanding each component's role, you'll gain insight into how modern software delivery pipelines are constructed and operated at scale.

In this pipeline, we'll leverage AWS's powerful developer tools along with Docker's container ecosystem to create a robust automation workflow. From source code management through to production deployment, each tool plays a crucial part in ensuring our application is built, tested, and deployed reliably and efficiently. As we progress through this workshop, you'll see how these services interact with each other to form a comprehensive CI/CD solution that can handle everything from code commits to production deployments.



**Note:** Below are the AWS CI/CD tools. If you're unfamiliar with these tools, please review them before continuing. Those who are already familiar with these tools may proceed to the Docker Intro section.


Let's explore each of these services in detail to understand their specific roles and how they contribute to our automated pipeline:

### AWS CodeConnections
AWS CodeConnections is a feature in the Developer Tools console to connect AWS resources such as AWS CodePipeline to external code repositories. Each connection is a resource that you can give to AWS services to connect to a third-party repository, such as Git or BitBucket. You can use a connection ARN in your stack templates for CodeBuild build projects, CodeDeploy applications, and pipelines in CodePipeline, without the need to reference stored secrets or parameters.

[Learn more about AWS CodeConnections..](https://docs.aws.amazon.com/dtconsole/latest/userguide/welcome-connections.html#welcome-connections-what-can-I-do)


---------

### AWS CodeBuild
AWS CodeBuild is a fully managed Continuous Integration (CI) service that:

* Compiles source code.
* Runs tests.
* Produces software packages that are ready to deploy.

With CodeBuild, you don’t need to provision, manage, and scale your own build servers. CodeBuild scales continuously and processes multiple builds concurrently, so your builds are not left waiting in a queue. You can get started quickly by using prepackaged build environments, or you can create custom build environments that use your own build tools. With CodeBuild, you are charged by the minute for the compute resources you use.

---------

Below are the common AWS CI/CD tools. If you're unfamiliar with these tools, please review them before continuing. Those who are already familiar with these tools may proceed to the next page. 

## AWS CodePipeline
![cicdpipeline](/images/cicdpipeline.png)

AWS CodePipeline is a fully managed Continuous Delivery (CD) service that helps you automate your release pipelines for fast and reliable application and infrastructure updates. CodePipeline automates the build, test, and deploy phases of your release process every time there is a code change, based on the release model you define. This enables you to rapidly and reliably deliver features and updates. You can easily integrate AWS CodePipeline with third-party services such as GitHub or with your own custom plugin. With AWS CodePipeline, you only pay for what you use. There are no upfront fees or long-term commitments. The CodePipeline has 2 types V1 and V2. Since October 24, 2023, it is recommended to use [CodePipeline V2](https://docs.aws.amazon.com/codepipeline/latest/userguide/pipeline-types.html)


---------

### AWS CodeDeploy
AWS CodeDeploy is a deployment service that automates application deployments to a variety of compute services such as Amazon EC2 instances, on-premises instances, serverless Lambda functions, or Amazon ECS services. CodeDeploy makes it easier for you to rapidly release new features, helps you avoid downtime during application deployment, and handles the complexity of updating your applications. You can use CodeDeploy to automate software deployments, eliminating the need for error-prone manual operations, and the service scales with your infrastructure so you can easily deploy to one instance or thousands.

---------

### Amazon Elastic Container Registry (ECR)
Amazon Elastic Container Registry (Amazon ECR) is an AWS managed container image registry service designed to store, manage, and deploy container images and artifacts. It provides a highly available and scalable infrastructure, eliminating the need for self-managed repositories. The service offers both private and public repositories, allowing organizations to host images securely or share them publicly. ECR seamlessly integrates with AWS services like IAM for access control, and container services such as ECS and EKS for streamlined deployments. Supporting both Docker and OCI images, ECR includes built-in vulnerability scanning to enhance security, making it a comprehensive solution for container image management.
