---
title: "Step 4: Build with Docker Build Cloud"
chapter: false
weight: 34
---

## **Prerequisites(TODO: Add package caching story)**

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

## **Enabling Docker Build Cloud via CLI**

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

3. **Build an image using Build Cloud:**
   ```sh
   docker buildx build --platform linux/amd64 --progress=plain -t my-app .
   ```

---

## **Configuring Build Settings**

### **Optimizing Builds with Caching Mechanisms**

Docker Build Cloud significantly improves build speed by utilizing **remote caching**. Instead of rebuilding unchanged layers, it retrieves them from the cloud, reducing redundant processing.

#### **Enable Remote Caching**

To use remote caching, store cache layers in a registry:

```sh
docker buildx build --cache-to=type=registry,ref=myrepo/cache --cache-from=type=registry,ref=myrepo/cache -t my-app .
```

### **Comparing Build Performance: Cloud vs. Local**

This is an example performance difference, your miledge might vary for your local machaine might be different.
| **Scenario** | **Local Build (BuildKit)** | **Docker Build Cloud** |
| --------------------- | -------------------------- | ---------------------- |
| Image Transfer | ~0.8s | ~1.2s |
| Node.js Metadata Load | ~0.7s | ~1.2s |
| Build Context Load | ~0.0s | ~0.0s |
| Base Image Pull | ~0.1s | ~0.2s |
| Nginx Image Pull | ~0.8s | ~2.6s |
| `npm install` | ~51.8s | ~50.4s |
| `npm run build` | ~27.0s | ~25.8s |
| Total Build Time | ~80s | ~76s |

#### **How Participants Can See the Efficiency Gain**

1. **Run a local build:**
   ```sh
   time docker build -t rent-a-room .
   ```
2. **Run a cloud build with caching enabled:**
   ```sh
   time docker buildx build --cache-from=type=registry,ref=myrepo/cache --cache-to=type=registry,ref=myrepo/cache -t rent-a-room .
   ```
3. **Compare build times** and observe the efficiency of caching and parallel execution.

---

## **Summary**

- **Docker Build Cloud speeds up builds** by using cloud-based execution.
- **Caching improves efficiency**, reducing redundant processing.
- **Comparing cloud vs. local builds** demonstrates significant time savings.
- **Ideal for large projects and CI/CD workflows**, ensuring faster deployments.

In the next section, we will explore how to utilize **Docker Hub**.
