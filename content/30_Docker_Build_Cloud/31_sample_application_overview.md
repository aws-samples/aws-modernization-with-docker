---
title: "Step 1: Sample Application Overview"
chapter: false
weight: 21
---

# **Sample Application Overview**

This module introduces the **Rent-A-Room** sample application, a frontend web app built with **React (frontend) and NGINX (webserver)**. The application is containerized using Docker and can be deployed both locally and in the cloud.

![Docker](/images/docker-aws.drawio.png)

## **Application Components**

- **Frontend:** React application served via NGINX.
- **Container Orchestration:** Managed with Docker Compose.

---

## **Getting the Sample Code**

The source code for this module is available on GitHub. Follow these steps to get started:

### **0. Fork the Repository(Optional)**

This is needed only if you want to update the frontend youself:

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

### **3. Creating the Dockerfile TODO: Add to the rent-a-room repo**

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

## **Next Steps**

Now that you have cloned the repository proceed to the next module to **build and containerize the application using Docker**.
