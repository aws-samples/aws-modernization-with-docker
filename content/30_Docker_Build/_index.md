---
title: "Build with Docker"
chapter: true
weight: 30
---


# Getting Started 
To start building with Docker, Click on "Docker Build Local" in the drop-down or "Docker Build Cloud" to continue.

* ...[running the workshop with Docker Build Local](../35_Docker_Build_Local/), or
* ...[running the workshop with Docker Build Cloud](../36_Docker_Build_Cloud/)

#TODO Update why we should use either docker build vs docker build cloud

# **1. Build with Docker**

Docker enables developers to build, ship, and run applications in containers efficiently. Traditionally, `docker build` is used for building container images locally, while **Docker Build Cloud** is a managed, cloud-based build service that provides **faster** and **scalable** image builds.

### **Why Use Docker Build Cloud?**

- **Faster builds:** Uses caching and parallel processing.
- **Scalability:** Leverages remote build execution for CI/CD pipelines.
- **Reduced local resource usage:** Offloads build operations to the cloud.
- **Consistent builds:** Provides a controlled build environment.

This guide provides **two approaches**:

1. **Local Build (`docker build`)**
2. **Cloud Build (`docker build cloud`)** (paid service)

We will use the **[Rent-A-Room](https://github.com/aws-samples/Rent-A-Room)** repository as an example. This is a React-based project using `react-scripts` for building and running the frontend.


TODO 
