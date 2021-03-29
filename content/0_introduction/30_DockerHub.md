+++
title = "Docker Hub"
chapter = false
weight = 30
+++

![Docker](/images/Moby-logo.png)

## Container Registries 

In the previous sections, we have gone over the core components of what a user needs to understand in order to utilize Docker as part of their development process. The next aspect to understanding how to fully utilize Docker is to understand how to efficently use Docker Hub as an image repository solution. We will be using Docker Hub as our image repository of choice for this workshop so it is important to understand the benefits of using Docker Hub. 

### What is Docker Hub?
Docker Hub is the world’s largest repository of container images with an array of content sources including container community developers, open source projects and independent software vendors (ISV) building and distributing their code in containers. Users get access to free public repositories for storing and sharing images or can choose subscription plan for private repos.

### Why should you use Docker Hub for your organizational needs?
Docker Hub is a great choice for sharing container images throughout your organization. Docker Hub provides a unified experience for finding, storing, and sharing container images. Millions of individual users and more than a hundred thousand organizations use Docker Hub for their container content needs. Docker Hub was designed to help bring together all the features that have proved to be successful with organizations of all sizes and is the reason why we are using Docker Hub for this workshop. Here are some of the benefits users get from using Docker Hub:

1. When using repositories within Docker Hub, you can iew recently pushed tags and automated builds on your repository page and easily filter through images as shown below:
![Docker](/images/Docker-Hub-Consolidation-Image-2.png)
2. As an organization Owner, see team permissions across all of your repositories at a glance and you can even add existing Docker Hub users to a team via their email if you don't have their Docker ID on hand. 
![Docker](/images/docker-hub-org.png)
3. Docker Hub can automatically test changes to your source code repositories using containers. You can enable Autotest on any Docker Hub repository to run tests on each pull request to the source code repository to create a continuous integration testing service. Enabling Autotest builds an image for testing purposes, but does not automatically push the built image to the Docker repository. We have included what this would look like in the UI below. 
![Docker](/images/index-dashboard.png)
4. Access to cerified publisher images and plugins that pass Docker quality, best practice, and support requirements. These tests include the following: 
    - Tested and supported on Docker Enterprise platform by verified publishers
    - Adhere to Docker’s container best practices
    - Pass a functional API test suite
    - Complete a vulnerability scanning assessment
    - Provided by partners with a collaborative support relationship
    - Display a unique quality mark “Docker Certified”

In the next section we will learn what Amazon ECS is as we will be using it as our container orchestration solution of choice for this workshop. Don't worry if you don't understand what that means yet as we will dive in to what that means in the next section!
