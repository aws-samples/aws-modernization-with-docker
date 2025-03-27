+++
title = "Step 1: Sample Application Overview"
chapter = false
weight = 21
+++
### Sample Application Overview
The sample application that we will use for this module is a frontend web app built with React (frontend) and NGINX (webserver). You will build, containerize, and deploy the Rent-A-Room application to AWS
### Application Components
* Frontend: React application served via NGINX.
* Container Orchestration: Managed with Docker Compose.
### Getting the Sample Code
The source code for our sample application is located on GitHub. Follow these steps to get started:
### 0. Fork the Repository(Optional)
This is needed only if you want to update the frontend youself:
Login to your GitHub account and fork the [GitHub Repo](https://github.com/aws-samples/Rent-A-Room). 
### 1. Clone the Repository
Run the following command to clone the repository to your local environment:
```
$ git clone https://github.com/aws-samples/Rent-A-Room.git
```
![githubsetup1](/images/githubsetup1.png)
### 2. Navigate to the Working Directory
```
$ cd Rent-A-Room
```
### 3. Creating the Dockerfile TODO: Add to the rent-a-room repo
Save the following content in a file named Dockerfile:
```
cat << 'EOF' > Dockerfile
# Build stage
FROM node:16-alpine as build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
# Production stage
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
EOF
```
### Next Steps
Now that you have cloned the repository proceed to the next module to build and containerize the application using AWS CodePipeline.
