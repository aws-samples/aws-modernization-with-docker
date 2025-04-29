---
title: "Run Application Locally"
chapter: false
weight: 34
---

# Run the application locally

Now that we have built the application locally, let's run our containerized application. Since we are not using Docker Compose, we will run the Docker container directly using `docker run`.

---

## **Start the Application**

Run the following command to start the **Rent-A-Room** frontend container:

```sh
docker run -d -p 3000:80 --name rent-a-room-container rent-a-room
```

This command:

- Runs the container in **detached mode** (`-d`).
- Maps **port 3000 on the host** to **port 80 inside the container** (`-p 3000:80`).
- Assigns the container the name `rent-a-room-container`.
- Uses the **built image** `rent-a-room`.

### **Test the Application in a Browser**

There will be a popup which will say "Your application running on port 3000 is available."
Click the "Open in Browser" Button.

If no popup, you can also access the application via the **PORTS** section and click on the **_Forwarded Address_**.
![Docker](/images/VSCode_PORTS.png)

---

**You should see the simple Rent A Room react app in your browser.**

If everything is set up correctly, the browser or curl command should return your frontend application. In the browser of your choice you can see the `rent a room` frontend application like this:
![Docker](/images/docker-frontend-built.png)

---

## **Inspect and Stop the Application**

### **Inspect Running Containers**

List all running containers:

```sh
docker ps
```

Check the logs for the container:

```sh
docker logs rent-a-room-container
```

### **Stop and Remove the Container**

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

In this step, we:

- Used **Docker** to start the application locally.
- Accessed the application via **localhost:3000**.
- Verified the container using `docker ps` and `docker logs`.
- Stopped and removed the container when finished.

In the next module, we will explore **Docker Build Cloud** and how to use it for faster and more efficient builds.
