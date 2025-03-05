---
title: "Step 3: Run Application Locally"
chapter: false
weight: 21
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

You can verify the running container with:

```sh
docker ps
```

---

### **Test the Application in a Browser**

Access the application at with this command in the Terminal:
```bash
echo "http://$(curl -s checkip.amazonaws.com):3000"
```

💡 **Pro Tip**:

- Mac users: Press ⌘ + click on the URL to open in browser
- Windows users: Press Ctrl + click on the URL to open in browser

**You should see the simple Rent A Room react app in your browser.**


If everything is set up correctly, the browser or curl command should return your frontend application.

---

## **Inspect and Stop the Application**

### **Inspect Running Containers**

- List all running containers:
  ```sh
  docker ps
  ```
- Check the logs for the container:
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
