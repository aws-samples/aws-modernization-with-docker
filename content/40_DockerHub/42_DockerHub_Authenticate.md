---
title: "Docker Hub CLI Authentication"
chapter: true
weight: 42
---

# Docker Hub CLI Authentication

## 🔐 CLI Login

1. Open your terminal
2. Use the Docker login command:

```bash
docker login -u replace-with-your-username
```

3. Enter the Personal Access Token as your password.

![Docker Login](/images/docker-cli-login.png)

## 📦 Sample Application

Clone the Rent-A-Room application:

```bash
git clone https://github.com/aws-samples/Rent-A-Room.git
cd Rent-A-Room
```

Create a Dockerfile in the root directory:

```dockerfile
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

## 🏷️ Building and Pushing

```bash
# REPLACE YOUR-DOCKER_USERNAME to set variable DOCKER_USERNAME
DOCKER_USERNAME=YOUR-DOCKER_USERNAME
```

```bash
# Confirm Docker username is properly set
echo $DOCKER_USERNAME
```

```bash
# Build the image
docker build -t rent-a-room .
```

```bash
# Tag for Docker Hub
docker tag rent-a-room $DOCKER_USERNAME/rent-a-room:v1.0
```

```bash
# Push to Docker Hub
docker push $DOCKER_USERNAME/rent-a-room:v1.0
```

![Push Progress](/images/push-progress.png)

## ✅ Testing Your Image

Pull and run your image locally:

```bash
docker run -d -p 3000:80 $DOCKER_USERNAME/rent-a-room:v1.0
```

Access the application at with this command in the Terminal:
```bash
echo "http://$(curl -s checkip.amazonaws.com):3000"
```

💡 **Pro Tip**:

- Mac users: Press ⌘ + click on the URL to open in browser
- Windows users: Press Ctrl + click on the URL to open in browser

**You should see the simple Rent A Room react app in your browser.**

## 🎯 Next Steps

Next, we'll automate this process using AWS CodePipeline.
