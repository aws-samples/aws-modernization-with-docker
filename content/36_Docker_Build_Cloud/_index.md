---
title: "Build with Docker Build Cloud"
chapter: true
weight: 34
---

## **Docker Build Cloud: Overview**

Docker Build Cloud is a powerful remote build service that accelerates container image creation by offloading build operations to Docker's cloud infrastructure.

### **Subscription Requirements**

Docker Build Cloud is available with:

- **Docker Pro** (\$9/month)
- **Docker Team** (\$15/user/month)
- **Docker Business** (\$24/user/month)

If you don't have a paid subscription, please use the [Docker Build Local](../35_Docker_Build_Local/) guide instead.

For subscription details, visit [Docker Pricing](https://www.docker.com/pricing/).

## **Prerequisites**

- Install **Docker Desktop**: [Download here](https://www.docker.com/get-started/)
- Ensure **Docker Desktop is running**
- Log in to **Docker Hub** (if not logged already via Docker Desktop):
  ```sh
  docker login
  ```
- Enable Docker Build Cloud in Docker Desktop (paid feature)
  - Open **Docker Desktop**
  - Go to **Settings > Features in Development**
  - Enable **Build Cloud**

---

### **Step 1: Clone the Project Repository**

First, clone the sample React application:

```bash
git clone https://github.com/aws-samples/Rent-A-Room.git
cd Rent-A-Room
```

### **Step 2: Create the Dockerfile**

Run the following command to save the content in the **Dockerfile**:

```bash
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

### **Step 3: Verify Docker Build Cloud Setup**

Ensure Docker Build Cloud is properly configured:

To verify buildx is available:

```bash
# Verify buildx is available
docker buildx version
```

To create and configure builder instance:

```bash
docker buildx create --name cloud-builder --use
```

the above will output:

```bash
cloud-builder
```

Bootstrap the builder instance using this command:

```bash
docker buildx inspect --bootstrap
```

You should see output indicating your builder is using the Docker Build Cloud driver like this:

```bash
+] Building 6.7s (1/1) FINISHED
 => [internal] booting buildkit                                                                                                      6.7s
 => => pulling image moby/buildkit:buildx-stable-1                                                                                   5.8s
 => => creating container buildx_buildkit_cloud-builder0                                                                             0.9s
Name:          cloud-builder
Driver:        docker-container
......
```

### **Step 4: Build Your Image with Docker Build Cloud**

Build the image using Docker Build Cloud:

```bash
docker buildx build --load --platform linux/amd64 -t rent-a-room .
```

Key options explained:

- **--load**: Imports the built image into your local Docker image store
- **--platform**: Specifies the target platform architecture
- **-t rent-a-room**: Tags the image with the name "rent-a-room"

### **Step 5: Verify Your Image**

Wait for step 4 to finish building, then check that your image was built successfully:

```bash
docker images | grep rent-a-room
```

If built successfully you will see output like this:

```bash
rent-a-room     latest            0b9c31de0251   3 seconds ago    111MB
```

### **Step 6: Run the Container**

Start a container using the image you built:

```bash
docker run -d -p 3000:80 --name rent-a-room-container rent-a-room
```

### **Step 7: Test the Application**

Access your application in a web browser at `http://localhost:3000`.
You should see the Rent-A-Room application running.
![Docker](/images/docker-frontend-built.png)

### **Step 8: Explore Build Acceleration**

Make a small change with the application like this:

```bash
# Make a small change to force a rebuild
echo "// Small change" >> src/App.js
```

Then time the cloud build

```bash
# Run the cloud build and time the building
time docker buildx build --load --platform linux/amd64 -t rent-a-room .
```

### **(Optional)Step 9: Advanced: Enable Remote Caching**

For even faster builds, especially in CI/CD environments, you could try something like below:

```bash
# Replace USERNAME with your Docker Hub username
docker buildx build \
  --cache-to type=registry,ref=USERNAME/rent-a-room-cache \
  --cache-from type=registry,ref=USERNAME/rent-a-room-cache \
  --load --platform linux/amd64 \
  -t rent-a-room .
```

### **Step 10: Stop and Remove the Application Container**

To stop the running container:

```sh
docker stop rent-a-room-container
```

To remove the container:

```sh
docker rm rent-a-room-container
```

---

## **Summary**

- **Docker Build Cloud speeds up builds** by using cloud-based execution.
- **Caching improves efficiency**, reducing redundant processing.
- **Comparing cloud vs. local builds** demonstrates significant time savings.
- **Ideal for large projects and CI/CD workflows**, ensuring faster deployments.

In the next section, we will explore how to utilize **Code Pipeline with Docker**.
