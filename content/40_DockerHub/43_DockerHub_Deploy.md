---
title: "Pushing Images to Docker Hub"
chapter: true
weight: 43
---

# 🚀 Pushing Images to Docker Hub

In this section, we will:
- ✅ **Tag our local image** for Docker Hub
- ✅ **Push the image** to our Docker Hub repository
- ✅ **Verify the push** in Docker Hub UI

---

## **1️⃣ Preparing the Image**

### **🔹 Image Tagging**
First, we'll tag our local image with our Docker Hub username:

```bash
docker tag rent-a-room $DOCKER_USERNAME/rent-a-room
```

Verify the new tag exists:

```bash
docker images | grep rent-a-room
```

![Docker Images Tagged](/images/docker-images-tagged.png)

### Push to Docker Hub

Push the tagged image to Docker Hub:

```bash
docker push $DOCKER_USERNAME/rent-a-room:latest
```

![Docker Push Progress](/images/docker-push-progress.png)

## Verification Steps

### Check Docker Hub UI

1. Log into [hub.docker.com](hub.docker.com)
2. Navigate to your repositories
3. Verify the new image appears

![Docker Hub Repository](/images/docker-hub-repo.png)

### CLI Verification
List your Docker Hub repositories:

```bash
curl -s -H "Authorization: Bearer $DOCKER_TOKEN" https://hub.docker.com/v2/repositories/$DOCKER_USERNAME/ | jq
```

## Best Practices

Remember to:
- Use meaningful tags for versioning
- Clean up local images regularly
- Keep access tokens secure

## Next Steps
Now that we've pushed our image:
1. Image is available globally
2. Ready for deployment
3. Proceed to AWS CodePipeline integration

[Next: Docker Scout](../50_Docker_Scout/_index.md)

## Next Steps
Now that our image is in Docker Hub:
1. **Let's scan our image for vulnerabilities**
2. **Ensure our container is secure for deployment**
3. **Move to the next section** to learn about Docker Scout security scanning 🔒
