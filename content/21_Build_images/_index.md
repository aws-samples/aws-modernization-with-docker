+++
title = "Module 1: Build a Multi container image with Docker Compose - Introduction"
chapter = true
weight = 21
+++

# Building Container Images with Docker Buildkit and Compose

**Estimated Completion Time: 30 minutes - 1 hour**

### Introduction

In this module we will learn how to build our own container images using the Docker Compose CLI tool, Docker Buildkit, and Docker Hub. We will also learn how to utilize Docker Compose files to help build our application. We will then test our application locally to see that it works. Let's discuss what Docker Compose is all about to help us understand the rest of this module. 

## Docker Compose
![Docker](/images/docker-compose.png)
So far, we have learned that we can use Dockerfiles to build our container images for **single container applications** but what do we do if we have an application that requires **multiple container images** to run properly? This is where Docker Compose comes in. 

Docker Compose is a tool for defining and running multi-container Docker applications. With Docker Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration file. Examples would be adding a database or a caching mechansim for your application. We will learn how to utililze these specific mechanisms in Module 2. 

## How does Docker Compose files differ from Dockerfiles?
Users that are new to containers and Docker can get really confused on when and where to use Dockerfiles and Docker Compose files. While there are situations where you could use one or the other, it really comes down to the complexity of your application you plan on deploying. In modern applications, it is very common to see applications use **both**. What this means is that you can have a Docker Compose file that will rely on a Dockerfile in order to build your application. In this section, we will learn how to utilize both in building our application so if this does not make sense at this moment, it certainly will by the end of this module. 

## Docker Compose Integration with Amazon ECS

The Docker Compose CLI enables developers to use native Docker commands to run applications in Amazon Elastic Container Service (Amazon ECS) when building cloud-native applications.

The integration between Docker and Amazon ECS allows developers to use the Docker Compose CLI to:

* Set up an AWS context in one Docker command, allowing you to switch from a local context to a cloud context and run applications quickly and easily
* Simplify multi-container application development on Amazon ECS using Compose files

Also see the [ECS integration architecture](https://docs.docker.com/cloud/ecs-architecture/), [full list of compose features](https://docs.docker.com/cloud/ecs-compose-features/) .

