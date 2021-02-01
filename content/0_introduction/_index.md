---
title: "Introduction"
weight: 5
chapter: true
draft: false
---
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