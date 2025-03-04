---
title: "Docker Hub Integration – CodePipeline Perspective"
chapter: false
weight: 41
---


# 🐳 Docker Hub Integration – CodePipeline Perspective

Now that we’ve built our Docker images using **Docker Build Cloud**, we need to **push them to Docker Hub**. This section will:
- ✅ **Append a `post_build` phase** to `buildspec.yml` to **automate pushing images**.
- ✅ **Explain what was added** so you understand its purpose.

---

## **1️⃣ Updating `buildspec.yml` to Push to Docker Hub**

In the previous section, **Docker Build Cloud** built the image but did **not push it**.  
Now, we’ll **add a `post_build` phase** to push the image to Docker Hub.

### **📌 Appending the `post_build` Phase**

Run the following command to append the **new `post_build` phase** **before** the `artifacts` section in `buildspec.yml`:

```bash
sed -i '/artifacts:/i\
  post_build:\
    commands:\
      - echo Build completed on `date`\
      - echo Pushing the Docker image to Docker Hub...\
      - docker push $DOCKER_USERNAME/myapp:latest\
' buildspec.yml
```

---

## **2️⃣ Verify the Changes**
Once the command runs, check the updated `buildspec.yml`:

```bash
cat buildspec.yml
```

---

## **3️⃣ Explanation of What We Added**

The **new `post_build` phase** ensures that after a successful build, the **Docker image is pushed to Docker Hub**.

```yaml
post_build:
  commands:
    - echo Build completed on `date`
    - echo Pushing the Docker image to Docker Hub...
    - docker push \$DOCKER_USERNAME/myapp:latest
```

### **📌 What Does This Do?**
✅ **`echo Build completed on `date``** – Logs the build completion time.  
✅ **`echo Pushing the Docker image to Docker Hub...`** – Provides a status message.  
✅ **`docker push \$DOCKER_USERNAME/myapp:latest`** – Pushes the built image to Docker Hub.  

---

## **4️⃣ Next Steps**
1️⃣ **Run the `sed` command** to modify `buildspec.yml`.  
2️⃣ **Use `cat buildspec.yml`** to verify the changes.  
3️⃣ **Commit the updated file to your repository**:

```bash
git add buildspec.yml
git commit -m "Added post_build phase to push images to Docker Hub"
git push origin main
```

---

## **✅ Summary**
By adding this `post_build` phase, your AWS **CodeBuild** step will now:

✅ **Build the image**  
✅ **Push it to Docker Hub**  

This ensures a fully **automated** process within **AWS CodePipeline**. 🚀  
