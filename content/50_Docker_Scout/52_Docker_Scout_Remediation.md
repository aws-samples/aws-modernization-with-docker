---
title: "Fixing Vulnerabilities in Docker Images"
chapter: false
weight: 52
---

# 🔧 Fixing Vulnerabilities in Docker Images

Now that we have scanned our image with **Docker Scout**, let’s **fix** the identified security issues.

---

## **1️⃣ Updating Our Dockerfile**
Based on our **Docker Scout scan results**, we need to **update base images** to remove vulnerabilities.

For example, if our **existing Dockerfile** is:

```dockerfile
# Vulnerable version
FROM node:16-alpine
```

We should **update it** to use a secure version:

```dockerfile
# Secure version
FROM node:18-alpine
```

If we detected issues in **nginx**, update:

```dockerfile
FROM nginx:1.21.6
```

To:

```dockerfile
FROM nginx:1.24.0
```

---

## **2️⃣ Rebuild and Rescan**
After updating the `Dockerfile`, rebuild the image:

```bash
docker build -t $DOCKER_USERNAME/myapp:latest .
```

Run another **Docker Scout scan** to confirm the issues are fixed:

```bash
docker scout cves $DOCKER_USERNAME/myapp:latest
```

---

## **📌 Next Steps**
Now that our image is secure, let’s **automate vulnerability scanning** in **AWS CodePipeline**!  
Move to the **next section** for CI/CD integration. 🚀
