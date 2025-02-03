---
title: "Docker Hub CLI Authentication"
chapter: true
weight: 42
---

# Docker Hub CLI Authentication

In this section, you'll learn how to authenticate with Docker Hub via CLI and manage your images.

## 🔐 CLI Login

1. Open your terminal
2. Use the `docker login` command:

```bash
docker login
```

3. Enter your credentials when prompted:
   - Username: Your Docker Hub username
   - Password: Your access token (not your account password)

![CLI Login](/images/docker-cli-login.png)

## 📦 Creating a Sample Image

Let's create a simple web application to containerize:

```bash
# Create a new directory
mkdir docker-workshop
cd docker-workshop
```

Create a Dockerfile:

```dockerfile
FROM nginx:alpine
COPY . /usr/share/nginx/html
```

Create a simple index.html:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Docker Workshop</title>
</head>
<body>
    <h1>Hello from Docker!</h1>
</body>
</html>
```

## 🏷️ Image Tagging

Build and tag your image:

```bash
# Build the image
docker build -t workshop-app .

# Tag for Docker Hub
docker tag workshop-app YOUR_USERNAME/workshop-app:v1.0
```

> Replace `YOUR_USERNAME` with your Docker Hub username

## ⬆️ Pushing to Docker Hub

Push your tagged image:

```bash
docker push YOUR_USERNAME/workshop-app:v1.0
```

## ✅ Verification

Verify your push:

1. Check local images:
```bash
docker images
```

2. View on Docker Hub:
   - Go to [Docker Hub](https://hub.docker.com)
   - Navigate to your repositories
   - You should see `workshop-app`

![Repository View](/images/repo-view.png)

## 🔍 Common Commands

| Command | Description |
|---------|-------------|
| `docker login` | Authenticate with Docker Hub |
| `docker build -t name .` | Build image with tag |
| `docker tag source:tag target:tag` | Create a new tag |
| `docker push name:tag` | Push to registry |
| `docker pull name:tag` | Pull from registry |

## 🎯 Next Steps

In the next section, we'll:
1. Set up a CI/CD pipeline
2. Automate image builds
3. Configure automatic pushes to Docker Hub