---
title: "Creating a Docker Hub Account"
chapter: true
weight: 41
---

# Creating a Docker Hub Account

In this section, you'll create a Docker Hub account which we'll use to store our container images.

## Why Docker Hub?

Docker Hub is a cloud-based registry service that allows you to:
- Store your container images
- Share your images with others
- Pull official images
- Automate your builds

## 🚀 Account Creation

1. Go to [Docker Hub](https://hub.docker.com)
2. Click "Sign Up" in the top right

![Sign Up](/images/dockerhub-signup.png)

3. Enter your details:
   - Username (you'll use this for your images)
   - Email address
   - Password

![Account Details](/images/dockerhub-details.png)

## 🔑 Personal Access Token Creation

For secure CLI access:

1. Log into Docker Hub
2. Click your username in top right

![Docker Settings](/images/dockerhub-settings.png)

3. Select "Account Settings"
4. Go to "Security"
5. Click "Personal Access Tokens"

![Token Creation](/images/token-creation.png)

6. Configure your token:
   - Name: "Docker Workshop Access"
   - Expiration Date: This is up to you!
   - Access permissions: Read & Write
   - Click "Generate"

![Token Settings](/images/token-settings.png)

> **Important**: Save your access token immediately! You won't be able to see it again.

## ✅ Verification

Ensure your account is ready:
1. Verify your email if required
2. Log in to Docker Hub website
3. Check you can see your account settings

## 🎯 Next Steps

In the next section, we'll:
1. Log in to Docker Hub from the CLI
2. Learn how to tag images
3. Push our first image