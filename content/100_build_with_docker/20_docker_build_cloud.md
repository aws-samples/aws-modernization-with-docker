+++
title = "Route 2: Docker Build Cloud"
chapter = false
weight = 21
+++

### **Prerequisites**

- Install **Docker Desktop**: [Download here](https://www.docker.com/get-started/)
- Create a **Docker Hub account** (needed for Docker Build Cloud)
- Authenticate with Docker CLI:
  ```sh
  docker login
  ```
- Enable Docker Build Cloud in Docker Desktop (paid feature)
  - Go to **Settings > Features in Development > Enable Build Cloud**

### **Enabling Docker Build Cloud via CLI**

1. **Check if Docker Build Cloud is enabled:**

   ```sh
   docker buildx version
   ```

   Ensure that it shows `buildx` support.

2. **Enable Build Cloud:**
   ```sh
   docker buildx create --name mybuilder --use
   docker buildx inspect --bootstrap
   ```

---

## ** Configuring Build Settings**

### **Dockerfile for Rent-A-Room**

Since Rent-A-Room is a React project, we need to build the frontend before creating a Docker image. A sample `Dockerfile` for the project:

```dockerfile
# Use an official Node.js runtime as a parent image
FROM node:16 as build
WORKDIR /app

# Copy package.json and install dependencies
COPY package.json .
RUN npm install --only=production

# Copy the entire project
COPY . .

# Build the project
RUN npm run build

# Use Nginx to serve the React app
FROM nginx:latest
COPY --from=build /app/build /usr/share/nginx/html

CMD ["nginx", "-g", "daemon off;"]
```

### **Common Build Configurations**

- **Setting build arguments:**
  ```sh
  docker build --build-arg ENV=production -t rent-a-room .
  ```
- **Using `.dockerignore`** to exclude unnecessary files:
  ```
  node_modules
  .git
  build
  coverage
  ```
