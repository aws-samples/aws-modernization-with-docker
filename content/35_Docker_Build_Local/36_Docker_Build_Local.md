---
title: "Docker Build Local"
chapter: false
weight: 33
---

# **Building the Image Locally (`docker build`)**

## **Prerequisites**

Before starting, ensure you have:

- **Docker installed and running**:
  - **Docker Desktop** ([Mac/Windows](https://www.docker.com/products/docker-desktop/))
  - **Docker Engine** ([Linux](https://docs.docker.com/engine/install/))
- **Git** installed to clone the repository
- At least 2GB of free disk space
- Basic familiarity with command-line operations

To verify Docker is properly installed and running:

```sh
docker -v
```

If you see information about your Docker version like below without errors, you're ready to proceed:

```sh
Docker version 27.3.1, build ce1223035a
```

## **Steps**

### **1. Clone the Repository**

Using git to clone the repository.

```sh
$ git clone https://github.com/aws-samples/Rent-A-Room.git
```

### **2. Navigate to the Working Directory**

If using a local development environment, navigate to the cloned repository:

```sh
$ cd Rent-A-Room
```

### **3. Creating the Dockerfile**

Run the following command to save the content in the **Dockerfile**:

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

#### **Want to understand more about the `Dockerfile`?**

This Dockerfile uses a multi-stage build process to create an optimized production image for a Node.js application served by Nginx. Let's break it down:

##### Build Stage

```bash
# Build stage
FROM node:12 as build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
```

- **FROM node:12 as build**: Starts with a Node.js 12 base image, naming this stage "build".
- **WORKDIR /app**: Sets the working directory in the container.
- **COPY package\*.json ./**: Copies package.json and package-lock.json (if present) to the working directory.
- **RUN npm install**: Installs the Node.js dependencies.
- **COPY . .**: Copies the rest of the application code into the container.
- **RUN npm run build**: Builds the application (typically creating a build or dist folder).

##### Build Stage

```bash
# Production stage
FROM nginx:1.14
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

**FROM nginx:1.14**: Starts a new stage using the Nginx 1.14 base image.
**COPY --from=build /app/build /usr/share/nginx/html**: Copies the built files from the previous stage to Nginx's serving directory.
**EXPOSE 80**: Informs Docker that the container will listen on port 80.
**CMD ["nginx", "-g", "daemon off;"]**: Starts Nginx in the foreground when the container runs.

---

### 4. Build the Docker Image

#### 4.1 **Run the local build command:**

Run the following to build the image:

```sh
docker build -t rent-a-room .
```

Then wait for the build to be completed.

#### 4.2 **Verify the built image:**

```sh
docker images
```

You willl be able to see the built image if the build was successful, the `docker images` will have output similar to this:

```sh
REPOSITORY  TAG      IMAGE ID       CREATED          SIZE
rent-a-room latest   6c024d6fbc2e   42 seconds ago   51.4MB
```

---

## **Optimizing Builds with BuildKit**

Docker **BuildKit** enhances build performance by enabling **parallel execution, improved caching, and reduced build times**.

### **BuildKit Availability**

BuildKit is now integrated as the **default builder** in:

- Docker Desktop (all versions)
- Docker Engine (version 23.0 and newer)

If you're using these modern Docker versions, you're already using BuildKit without any additional configuration!

### **Benefits of BuildKit**

- **Faster builds** due to **parallel execution** of build steps
- **Smarter dependency analysis** for more efficient layer caching
- **Security improvements** with **automatic secrets masking**
- **Mountable cache volumes** to speed up package managers
- **Multi-platform builds** from a single host

### **For Older Docker Versions Only**

If using Docker versions older than 23.0, you can manually enable BuildKit:

export DOCKER_BUILDKIT=1
docker build -t rent-a-room .
Getting Started with Docker
New to Docker? Download and install Docker Desktop from the [official site for getting started](https://www.docker.com/get-started/)

---

## **Summary**

- **Built a Docker image locally** using `docker build`.
- **Optimized builds with BuildKit** for faster performance.
- **Pushed the Docker image to Docker Hub** for deployment.

Next, we will explore **running the application locally** and view the built frontend application we've built.

---
