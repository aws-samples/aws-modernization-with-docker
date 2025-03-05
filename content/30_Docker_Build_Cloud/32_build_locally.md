---
title: "Build Image Locally"
chapter: false
weight: 21
---

# **Building the Image Locally (`docker build`)**

## **Steps**

### ** Fork the Repository**

Login to your GitHub account and fork the [GitHub Repo](https://github.com/aws-samples/Rent-A-Room/fork).

### **1. Clone the Repository**

Once the fork had been created, click on the Green `Code` button and copy your repo's URL and update the `REPO_URL` variable with your Repo URL:

```sh
REPO_URL=replae-me-with-your-repo-url.git
```

```sh
echo $REPO_URL
$ git clone $REPO_URL
```

### **2. Navigate to the Working Directory**

If using a local development environment, navigate to the cloned repository:

```sh
$ cd Rent-A-Room
```

### **3. Creating the Dockerfile**

Save the following content in a file named **Dockerfile**:

```
cat << 'EOF' > Dockerfile
# Build stage
FROM node:12 as build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Production stage
FROM nginx:1.14
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
EOF
```

---


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

## **Summary**

- **Built a Docker image locally** using `docker build`.
- **Optimized builds with BuildKit** for faster performance.
- **Pushed the Docker image to Docker Hub** for deployment.

Next, we will explore **running the application locally** and **leveraging Docker Build Cloud** for even **faster and more efficient builds**.

---
