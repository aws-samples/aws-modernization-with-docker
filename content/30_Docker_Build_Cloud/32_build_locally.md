---
title: "Step 2: Build Image Locally"
chapter: false
weight: 21
---

# **Building the Image Locally (`docker build`)**

## **Steps**

1. **Clone the repository(If not already done):**
   ```sh
   git clone https://github.com/aws-samples/Rent-A-Room.git
   cd Rent-A-Room
   ```
2. **Run the local build command:**
   Run the following to build the image:
   ```sh
   docker build -t rent-a-room .
   ```
   Then wait for the build to be completed.
3. **Verify the built image:**
   ```sh
   docker images
   ```
   You willl be able to see the built image if the build was successful.
   ```sh
   REPOSITORY  TAG      IMAGE ID       CREATED          SIZE
   rent-a-room latest   6c024d6fbc2e   42 seconds ago   51.4MB
   ```

---

## **Optimizing Builds with BuildKit**

Docker **BuildKit** enhances build performance by enabling **parallel execution, improved caching, and reduced build times**.

### **Enable BuildKit (Optional)**

Before running `docker build`, enable **BuildKit** for faster builds:

```sh
export DOCKER_BUILDKIT=1
docker build -t rent-a-room .
```

### **Benefits of BuildKit**

- **Faster builds** due to **parallel execution**.
- **Better caching** and reuse of unchanged layers.
- **Security improvements** with **automatic secrets masking**.

---

## **Creating a Build Stage in a CI/CD Pipeline**

To integrate Docker image builds into a **CI/CD pipeline**, use **GitHub Actions** or **AWS CodeBuild**.

### **Docker Hub Image Deployment(TODO: work with pjv)**

```sh
# REPLACE YOUR-DOCKER_USERNAME to set variable DOCKER_USERNAME
DOCKER_USERNAME=YOUR-DOCKER_USERNAME
```

```sh
# Confirm Docker username is properly set
echo $DOCKER_USERNAME
```

```sh
# Tag the image for Docker Hub
docker tag rent-a-room $DOCKER_USERNAME/rent-a-room:v1.0
```

```sh
# Push the image to Docker Hub
docker push $DOCKER_USERNAME/rent-a-room:v1.0
```

---

## **Summary**

- **Built a Docker image locally** using `docker build`.
- **Optimized builds with BuildKit** for faster performance.
- **Pushed the Docker image to Docker Hub** for deployment.

Next, we will explore **running the application locally** and **leveraging Docker Build Cloud** for even **faster and more efficient builds**.

---
