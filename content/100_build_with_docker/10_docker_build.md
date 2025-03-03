+++
title = "Route 1: Docker Build"
chapter = false
weight = 21
+++

## **4. Building the Image Locally (`docker build`)**

### **Steps**

1. **Clone the repository:**
   ```sh
   git clone https://github.com/aws-samples/Rent-A-Room.git
   cd Rent-A-Room
   ```
2. **Run the local build command:**
   ```sh
   docker build -t rent-a-room .
   ```
3. **Verify the built image:**
   ```sh
   docker images
   ```

---

## **5. Building the Image with Docker Build Cloud**

1. **Enable Docker Build Cloud** (Ensure `docker buildx` is set up)
2. **Build and push the image using Build Cloud:**
   ```sh
   docker buildx build --builder mybuilder --push -t my-dockerhub-user/rent-a-room .
   ```
3. **Verify build status on Docker Hub.**

---

## **6. Creating a Build Stage in a CI/CD Pipeline**

To integrate Docker image builds into a **CI/CD pipeline**, use **GitHub Actions** or AWS CodeBuild.

### **GitHub Actions Example**

Create `.github/workflows/docker-build.yml`:

```yaml
name: Docker Build

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: 16

      - name: Install dependencies
        run: npm install

      - name: Build project
        run: npm run build

      - name: Login to Docker Hub
        run: echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin

      - name: Build and push Docker image
        run: |
          docker build -t my-dockerhub-user/rent-a-room .
          docker push my-dockerhub-user/rent-a-room
```

This automates image builds and pushes to **Docker Hub** when changes are pushed to `main`. The React app is built before packaging into a Docker image.

---
