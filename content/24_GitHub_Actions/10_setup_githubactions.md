+++
title = "Step 1: Set up GitHub Actions"
chapter = false
weight = 21
+++

### GitHub Actions Set Up
The first step in creating our GitHub Actions workflow is to outline exactly what our workflow will look like before we build it out for our appliction. In module 1, we learned how to build and push images using Docker Compose to Docker Hub as our image repository of choice. Module 2 covered how to take the image we built in module 1 and deploy it to Amazon ECS. 

While it is important to understand how to do these tasks step by step, in a production environment where you are potentially working with multiple teams and organizations this can add unnecessary operational overhead to your workflow process. Let's go ahead and look at the workflow we will be using to build a GitHub Actions workflow that will automate building and pushing our images to Docker Hub.

Click on the ```.github/workflows``` folder in your Cloud9 file directory and you should see a file named ```docker-hub.yaml``. This is the file that we will be using to build a GitHub Actions workflow that will automate the process of building and pushing our Compose application to Docker Hub. Let's take a look at what this file is doing. 

```
name: Docker Compose CI CD to DockerHub

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  docker-build-push-backend:
    runs-on: ubuntu-latest
    steps:
      -
        name: Checkout
        uses: actions/checkout@v2
      -
        name: Set up QEMU
        uses: docker/setup-qemu-action@v1
      -
        name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1
      -
        name: Login to DockerHub
        uses: docker/login-action@v1 
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      -
        name: Build and push
        uses: docker/build-push-action@v2
        with:
          file: ./backend/Dockerfile
          push: true
          tags: ${{ secrets.DOCKERHUB_USERNAME }}/backend:${{ github.sha }}
  docker-build-push-frontend:
    runs-on: ubuntu-latest
    steps:
      -
        name: Checkout
        uses: actions/checkout@v2
      -
        name: Set up QEMU
        uses: docker/setup-qemu-action@v1
      -
        name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1
      -
        name: Login to DockerHub
        uses: docker/login-action@v1 
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      -
        name: Build and push
        uses: docker/build-push-action@v2
        with:
          file: ./frontend/Dockerfile
          push: true
          tags: ${{ secrets.DOCKERHUB_USERNAME }}/frontend:${{ github.sha }}
```

Every time we push our image to Docker Hub using the Docker Compose CLI it will kick off our GitHub Actions as defined under the following code block:

```
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
```

The next code block defines the various jobs that we have defined to run every time an image is pushed to Docker Hub:
```
jobs:
  docker-build-push-backend:
    runs-on: ubuntu-latest
    steps:
      -
        name: Checkout
        uses: actions/checkout@v2
      -
        name: Set up QEMU
        uses: docker/setup-qemu-action@v1
      -
        name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1
      -
        name: Login to DockerHub
        uses: docker/login-action@v1 
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      -
        name: Build and push
        uses: docker/build-push-action@v2
        with:
          file: ./backend/Dockerfile
          push: true
          tags: ${{ secrets.DOCKERHUB_USERNAME }}/backend:${{ github.sha }}
  docker-build-push-frontend:
    runs-on: ubuntu-latest
    steps:
      -
        name: Checkout
        uses: actions/checkout@v2
      -
        name: Set up QEMU
        uses: docker/setup-qemu-action@v1
      -
        name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1
      -
        name: Login to DockerHub
        uses: docker/login-action@v1 
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      -
        name: Build and push
        uses: docker/build-push-action@v2
        with:
          file: ./frontend/Dockerfile
          push: true
          tags: ${{ secrets.DOCKERHUB_USERNAME }}/frontend:${{ github.sha }}
```

Since we already have our project set up from the previous modules please follow these instructions to make sure we can access Docker Hub from the workflow that we'll be creating in this module.

1. Add your Docker ID as a secret to GitHub. Navigate to your GitHub repository and click Settings > Secrets > New secret.

![Docker](/images/githubactions-step1.png)
2. Create a new secret with the name ```DOCKER_HUB_USERNAME``` and your Docker ID as the value. You should see the following if you performed this step correctly.
![Docker](/images/githubactions-step2.png)
3. Create a new Personal Access Token (PAT). To create a new token, go to [Docker Hub Settings](https://hub.docker.com/settings/security) and then click **New Access Token**. We'll call this token awsdockerworkshop. Copy the PAT that populates once you create the token. 
![Docker](/images/githubaction-step3.png)
4. Go back to the GitHub secrets page from step 2 and add the PAT you copied from step 3 with the name ```DOCKER_HUB_ACCESS_TOKEN``` and if once you've done that you should see the following:
![Docker](/images/githubactions-step4.png)

### Set up the GitHub Actions workflow
n the previous section, we created a PAT and added it to GitHub to ensure we can access Docker Hub from any workflow. Now, let’s set up our GitHub Actions workflow to build and store our images in Hub. We can achieve this by creating two Docker actions:

1. The first action enables us to log in to Docker Hub using the secrets we stored in the GitHub Repository.
2. The second one is the build and push action.

**Note that in our configuration file that we have set the push flag to true so that we'll have the ability to push changes to any of our application code. We have also added a tag as an environment variable that authenticates us by referencing the secrets we created in the steps above.**

The next step is to go into our GitHub repository and create our first GitHub Action!

- Go to your repository in GitHub and then click **Actions** > **New workflow**.

![Docker](/images/githubactions-step5.png)

- Click set up a workflow yourself and add the following content from the ```docker-hub.yaml``` file in ```.github/workflows``` and you should see the following:
![Docker](/images/githubactions-step6.png)

- The last step is to test that our new GitHub Actions workflow works. Commit the new file and watch the workflow run. If everything worked out you should see the following:
![Docker](/images/githubactions-final.png)

### Summary
In this section, we have walked through how to set up a GitHub Actions workflow and tested that our initial workflow worked by committing a new file to our repository. In the next section, we will show you how to utilize the workflow you created in this section to our Compose application that we created in Modules 1 and 2. 