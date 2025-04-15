---
title: "Build with Docker"
chapter: true
weight: 30
---

# Getting Started

Want to skip the introduction? Choose your preferred Docker build method to continue:

- [Run the workshop with Docker Build Local](../35_Docker_Build_Local/)
- [Run the workshop with Docker Build Cloud](../36_Docker_Build_Cloud/)

## Docker Build vs Docker Build Cloud

Docker enables developers to efficiently build, ship, and run containerized applications. You have two main options for building container images:

### Docker Build Cloud Benefits

- **Faster builds:** Leverages cloud-based caching and parallel processing
- **Scalability:** Seamlessly handles large-scale build operations for CI/CD pipelines
- **Resource efficiency:** Eliminates local hardware constraints by offloading build operations
- **Build consistency:** Provides standardized, controlled build environments

### Docker Build Local Benefits

- **Internet independence:** Build without requiring external services or connectivity
- **Zero cost:** No additional expenses beyond your local hardware resources
- **Complete control:** Full autonomy over your build environment configuration
- **Privacy:** Keep sensitive code and assets entirely on your infrastructure

## Performance Comparison

Watch this side-by-side comparison between local and cloud builds:

<video width="100%" controls>
  <source src="/images/build-cloudv-video-1080.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

For detailed performance metrics and an ROI calculator, visit the [Docker Build Cloud product page](https://www.docker.com/products/build-cloud/). The calculator demonstrates potential savings with Docker Build Cloud (example below shows estimated savings for a team of 100 engineers):

![Docker Build Cloud Savings](/images/docker-local-v-cloud-esitmated-saving.png)

## Pricing Information

Docker Build Cloud availability depends on your subscription tier:

| Subscription Level | Price          | Build Minutes        |
| ------------------ | -------------- | -------------------- |
| Docker Personal    | Free           | Trial period only    |
| Docker Pro         | $9/month       | Standard allocation  |
| Docker Team        | $15/user/month | Increased allocation |
| Docker Business    | $24/user/month | Maximum allocation   |

For complete pricing details, visit the [Docker Pricing page](https://www.docker.com/pricing/).

## Workshop Example Project

This workshop uses the [Rent-A-Room](https://github.com/aws-samples/Rent-A-Room) repository as our example application. This is a React-based project using `react-scripts` for building and running the frontend.

## **Choose Your Build Method**

Select which Docker build approach you'd like to use:

- **[Continue with Docker Build Local](./36_Docker_Build_Local/)** - The traditional approach using your local machine resources (free, works offline)
- **[Switch to Docker Build Cloud](../36_Docker_Build_Cloud/)** - The cloud-based approach for faster builds (requires paid subscription or using free trial)

