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

### **1. GitHub Authentication**

Authenticate with GitHub using the pre-installed GitHub CLI:

```sh
# Login to GitHub
gh auth login
```

You will be guided through several prompts:

1. Select **GitHub.com**
2. Select **HTTPS** as your preferred protocol
3. When asked "Authenticate Git with your GitHub credentials?": Enter **Y**
4. Select **Login with a web browser**
5. Copy the one-time code shown in your terminal
6. Press Enter to open the browser
7. Paste the code in GitHub and authorize access

![Github Cli Auth](/images/gh-auth.png)

### **2.  Fork and Clone the Repository**

Instead of cloning directly, we'll fork the repository first:

```sh
# Fork and clone the repository
gh repo fork aws-samples/Rent-A-Room --clone=true

# Change to the repository directory
cd Rent-A-Room
```

### **3. Creating the Dockerfile**

Run the following command to save the content in the **Dockerfile**:

```bash
cat << 'EOF' > Dockerfile
# Build stage
FROM node:12 as build
WORKDIR /app
ENV DISABLE_ESLINT_PLUGIN=true
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:1.14
COPY --from=build /app/build /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
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
ENV DISABLE_ESLINT_PLUGIN=true
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

- **FROM nginx:1.14**: Starts a new stage using the Nginx 1.14 base image.
- **COPY --from=build /app/build /usr/share/nginx/html**: Copies the built files from the previous stage to Nginx's serving directory.
- **EXPOSE 80**: Informs Docker that the container will listen on port 80.
- **CMD ["nginx", "-g", "daemon off;"]**: Starts Nginx in the foreground when the container runs.

##### Create the nginx.conf file

Create the Nginx configuration file with the following command:

```bash
cat << 'EOF' > nginx.conf
server {
    listen 80;
    server_name localhost;

    root /usr/share/nginx/html;
    index index.html;

    # Support for SPA routing
    location / {
        try_files $uri $uri/ /index.html;
        add_header Cache-Control "no-cache";
    }

    # Serve static files
    location /static/ {
        expires 1y;
        add_header Cache-Control "public";
    }

    # Handle all API requests
    location /api/ {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
EOF
```

##### Configuring for Multiple Environments(Vs-code + localhost)

```bash
# Create development environment file:
cat << 'EOF' > .env.development
REACT_APP_API_URL=/proxy/3000
EOF

# Create production environment file:
cat << 'EOF' > .env.production
REACT_APP_API_URL=/api
EOF
```

##### Update Configuration Files

Run the following commands to update both package.json and App.js:

```bash
# Add environment check before the return statement
sed -i '/function App() {/a\  const isVSCodeServer = window.location.href.includes('\''cloudfront.net'\'');\n  const basename = isVSCodeServer ? '\''/proxy/3000'\'' : '\''/'\'';' src/App.js

# Update Router to use basename
sed -i 's/<Router>/<Router basename={basename}>/' src/App.js

# Update package.json to add homepage
sed -i '/"private": true,/a\  "homepage": ".",' package.json
```

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

You will be able to see the built image if the build was successful, the `docker images` will have output similar to this:

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
