+++
title = "CI/CD Tools and Services"
chapter = true
weight = 21
+++

# Understanding CI/CD Tools and Services


Before we begin building our CI/CD pipeline, let's understand the core services and tools we'll be using throughout this workshop. Our pipeline will utilize a suite of AWS services and Docker tools that work together seamlessly to automate the software delivery process. By understanding each component's role, you'll gain insight into how modern software delivery pipelines are constructed and operated at scale.

In this pipeline, we'll leverage AWS's powerful developer tools along with Docker's container ecosystem to create a robust automation workflow. From source code management through to production deployment, each tool plays a crucial part in ensuring our application is built, tested, and deployed reliably and efficiently. As we progress through this workshop, you'll see how these services interact with each other to form a comprehensive CI/CD solution that can handle everything from code commits to production deployments.

Let's explore each of these services in detail to understand their specific roles and how they contribute to our automated pipeline:


### AWS CodeConnections
AWS CodeConnections is a feature in the Developer Tools console to connect AWS resources such as AWS CodePipeline to external code repositories. Each connection is a resource that you can give to AWS services to connect to a third-party repository, such as Git or BitBucket. You can use a connection ARN in your stack templates for CodeBuild build projects, CodeDeploy applications, and pipelines in CodePipeline, without the need to reference stored secrets or parameters.


### AWS CodeBuild
AWS CodeBuild is a fully managed Continuous Integration (CI) service that:

* Compiles source code.
* Runs tests.
* Produces software packages that are ready to deploy.

With CodeBuild, you don’t need to provision, manage, and scale your own build servers. CodeBuild scales continuously and processes multiple builds concurrently, so your builds are not left waiting in a queue. You can get started quickly by using prepackaged build environments, or you can create custom build environments that use your own build tools. With CodeBuild, you are charged by the minute for the compute resources you use.

## AWS CodePipeline
![Docker](/images/cicdpipeline.png)

AWS CodePipeline is a fully managed Continuous Delivery (CD) service that helps you automate your release pipelines for fast and reliable application and infrastructure updates. CodePipeline automates the build, test, and deploy phases of your release process every time there is a code change, based on the release model you define. This enables you to rapidly and reliably deliver features and updates. You can easily integrate AWS CodePipeline with third-party services such as GitHub or with your own custom plugin. With AWS CodePipeline, you only pay for what you use. There are no upfront fees or long-term commitments. The CodePipeline has 2 types V1 and V2. Since October 24, 2023, it is recommended to use [CodePipeline V2](https://docs.aws.amazon.com/codepipeline/latest/userguide/pipeline-types.html)


### AWS CodeDeploy
AWS CodeDeploy is a deployment service that automates application deployments to a variety of compute services such as Amazon EC2 instances, on-premises instances, serverless Lambda functions, or Amazon ECS services. CodeDeploy makes it easier for you to rapidly release new features, helps you avoid downtime during application deployment, and handles the complexity of updating your applications. You can use CodeDeploy to automate software deployments, eliminating the need for error-prone manual operations, and the service scales with your infrastructure so you can easily deploy to one instance or thousands.

### Amazon Elastic Container Registry (ECR)
Amazon Elastic Container Registry (Amazon ECR) is an AWS managed container image registry service designed to store, manage, and deploy container images and artifacts. It provides a highly available and scalable infrastructure, eliminating the need for self-managed repositories. The service offers both private and public repositories, allowing organizations to host images securely or share them publicly. ECR seamlessly integrates with AWS services like IAM for access control, and container services such as ECS and EKS for streamlined deployments. Supporting both Docker and OCI images, ECR includes built-in vulnerability scanning to enhance security, making it a comprehensive solution for container image management.

### Docker Build Cloud
Docker Build Cloud is a service that lets you build your container images faster, both locally and in CI. Builds run on cloud infrastructure optimally dimensioned for your workloads, no configuration required. The service uses a remote build cache, ensuring fast builds anywhere and for all team members.

### Docker Scout
Docker Scout is a comprehensive security solution designed to address vulnerabilities in container images, which are composed of layers and software packages that can pose security risks to containers and applications. It enhances software supply chain security by analyzing images and creating a Software Bill of Materials (SBOM) - an inventory of all components. This SBOM is continuously checked against an updated vulnerability database to identify potential security weaknesses. Users can access Docker Scout through multiple interfaces, including Docker Desktop, Docker Hub, Docker CLI, and the Docker Scout Dashboard, while also leveraging its integration capabilities with third-party container registries and CI platforms.

### Docker Hub
Docker Hub is a robust container registry platform that streamlines development by providing extensive tools for storing, managing, and sharing Docker images. It offers developers access to pre-built images and assets to accelerate development workflows, while featuring seamless tool integration for enhanced productivity and reliable application deployment. The platform includes key features such as unlimited public repositories, private repositories, workflow automation through webhooks, integration with GitHub and Bitbucket, support for concurrent and automated builds, and a collection of trusted, secure content. This combination of features makes Docker Hub an essential resource for container-based development and deployment.

### Testcontainers
Testcontainers is a popular open source library designed to support integration testing by providing lightweight, disposable instances of common databases, web browsers, or any service that can run in a Docker container. It allows developers to write tests that interact with real instances of external resources, rather than relying on mocks or stubs.
