---
title: "Step 1: Sample Application Overview"
chapter: false
weight: 21
---

# **Sample Application Overview**

This module introduces the **Rent-A-Room** sample application, a full-stack web app built with **Flask (backend), MySQL (database), and NGINX (webserver)**. The application is containerized using Docker and can be deployed both locally and in the cloud.

![Docker](/images/application-overview.png)

## **Application Components**

- **Frontend:** Static files served via NGINX.
- **Container Orchestration:** Managed with Docker Compose.

---

## **Getting the Sample Code**

The source code for this module is available on GitHub. Follow these steps to get started:

### **1. Fork the Repository**

Login to your GitHub account and fork the repo:

[GitHub Repo](https://github.com/aws-samples/Rent-A-Room)

### **2. Clone the Repository**

Run the following command to clone the repository to your local environment:

```sh
$ git clone https://github.com/your-github-username/Rent-A-Room.git
$ cd Rent-A-Room
```

### **3. Navigate to the Working Directory**

If using AWS Cloud9 or a local dev environment, navigate to the cloned repository:

```sh
cd ~/environment/Rent-A-Room
```

Your Cloud9 file directory should now resemble the following:

![Docker](/images/docker-clone.png)

---

## **Building and Pushing the Docker Image**

To containerize the application, follow these steps:

### **1. Set up your Docker username**

```sh
DOCKER_USERNAME=YOUR-DOCKER_USERNAME
```

### **2. Confirm Docker username is properly set**

```sh
echo $DOCKER_USERNAME
```

### **3. Build the Docker image**

```sh
docker build -t rent-a-room .
```

### **4. Tag the image for Docker Hub**

```sh
docker tag rent-a-room $DOCKER_USERNAME/rent-a-room:v1.0
```

### **5. Push the image to Docker Hub**

```sh
docker push $DOCKER_USERNAME/rent-a-room:v1.0
```

![Push Progress](/images/push-progress.png)

### **6. Test the Docker Image Locally**

Run the container locally:

```sh
docker run -d -p 3000:80 $DOCKER_USERNAME/rent-a-room:v1.0
```

Retrieve the application URL:

```sh
echo "http://$(curl -s checkip.amazonaws.com):3000"
```

---

## **Next Steps**

Now that you have cloned the repository and containerized the application, proceed to the next module to **deploy it in a cloud environment**.
