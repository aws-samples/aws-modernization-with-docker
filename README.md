# AWS Modernization with Docker

Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. With Docker, you can manage your infrastructure in the same ways you manage your applications. By taking advantage of Docker’s methodologies for shipping, testing, and deploying code quickly, you can significantly reduce the delay between writing code and running it in production.

### Welcome

In this workshop you will learn how to build and deploy applications with Docker and Amazon ECS using the Docker Compose feature developed in partnership by AWS and Docker. 

### Learning Objectives
- Deploy an application using the Docker ECS Integration
- Build your own image using Docker Buildkit and how to deploy that image to Amazon ECS.
- Container security using AWS Secrets Manager
- CI/CD using the Amazon ECS plugin for GitHub Actions
- Deploy serverless containers using AWS Fargate

### Instruction to run workshop on your own: 

This page is build with Hugo, so you'll need to install it - https://gohugo.io/getting-started/installing/

Next, clone this repo: 

```
git clone git@github.com:aws-samples/aws-modernization-with-docker.git
```

Ensure that you have also cloned the submodules:

```
git submodule init
git submodule update
```

Start the server with Hugo:
```
hugo server -D
```

Go to localhost: to view the content in your web browser of choice
