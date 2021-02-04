+++
title = "Docker Architecture"
chapter = false
weight = 20
+++

In the previous section, we talked about Docker Engine and how it provides the underlying infrastructure for things like the Docker API, Docker CLI, and the various plugins like the Amazon ECS plugin that we will dive into during this workshop. It is important to lay the foundation for understanding what powers the various Docker tools as we will be using them in this workshop. In this section, we will go over what we call a layered architecture which helps us understand how Docker builds and deploys applications from container images otherwise known as the infamous Dockerfile. 

### Dockers Layered Architecture
