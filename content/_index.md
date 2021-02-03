---
title: "AWS Modernization with Docker"
chapter: true
weight: 1
---
![Docker] (/images/docker-cloud-twitter-card.png)

# Container Best Pracitces with Docker and AWS

Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. With Docker, you can manage your infrastructure in the same ways you manage your applications. By taking advantage of Docker’s methodologies for shipping, testing, and deploying code quickly, you can significantly reduce the delay between writing code and running it in production.

### Welcome

In this workshop you will learn how to build and deploy applications with Docker and Amazon ECS. 

### Learning Objectives
- Deploy an application using the Docker ECS Integration
- Build your own image using Docker Buildkit and how to deploy that image to Amazon ECS.
- Container security using AWS Secrets Manager
- CI/CD using the Amazon ECS plugin for GitHub Actions
- Collect Docker metrics with Prometheus

## Outline 
# Docker ECS Integration

1. Deploy Using Docker ECS integration
    - Docker Context Overview
    - Create and Manage Contexts
    - Deploy single application using ECS context

2. Build you own images with Buildkit and deploy to ECS
    - Clone sample repo
    - Application Overview
    - Building Images using BuildKit
    - Push images to hub
    - Run locally
    - Deploy to ecs
    - Test app
    - View logs
    - View entities in aws console
    - Scale Cluster
    - Verify that we now have two backend services 
    - Test app
    - Teardown cluster

3. Securing your containers with AWS Secrets Manager
    - Refactor backend code to connect to MongoDB
    - Change to private repo in hub
    - Create secret on ecs with docker cli
    - Use secret in compose file
    - Run and Test locally
    - Deploy to ECS
    - Test app
    - Teardown cluster

4. Demonstrating ECS plugin for CI/CD using GitHub Actions

5. Collect Docker metrics with Prometheus