---
title: "Module 3"
chapter: true
weight: 23
---

# CI/CD with Codebuild, Codepipeline, and Docker Compose

**Estimated Completion Time: 1 Hour**

In the previous sections we have learned how to utilize Docker Compose, BuildKit, and Docker Hub to build our application as a container image and push that image to a container image repository. We then learned how to take that image and deploy it to Amazon ECS using the Docker Compose integration with ECS. In this section, we will learn how to automate the deployment process so that you don't have to conduct each one of these steps manually by building a CI/CD pipeline with AWS CodeBuild and AWS CodePipeline. 

### CI/CD Overview
One of the most important things to understand before automating your deployment workflows is to understand the difference between CI (continuous integration) and CD (continuous deployment) as this concept can be confusing for users that are not familar with CI/CD concepts. Let's go ahead and break down the differences before we start building out our pipelines. 

### Continuous Integration (CI) 
Continuous Integration (CI) is the concept of continuously integrating your code changes to a central repository where automated builds and tests run to ensure that there are no integration challenges that can happen right before those changes are merged into the release branch. We have all heard nightmare stories about code changes that include bugs which cause delays in feature/product releases so it is crucial that you carefully define what your test and build process to have an efficient CI system in place. 

### Continuous Development (CD) and Continuous Deployment (CD)

Continuous Development (CD) is the process of automatically deploying all code changes to a testing and/or production environment after the build stage of your workflow is complete. This means that on top of automated testing, you have an automated release process and you can deploy your application any time by clicking a button. You have the option to decide how often you want to deploy your code but we recommend that you deploy your code to production in small batches as early as possible in cases where you need to troubleshoot. 

CD can also be known as continuous deployment which is different than continuous development as defined above. Continuous deployment actually goes a step further than continuous development in that every change that passes all stages of your production pipeline is released to your end users or customers. There is no human intervention and only a failed test will prevent a new change to be deployed to production. The benefit here is that you accelerate deployment times and your developer teams can focus on building software as they can see their work go live instantenously. 

In the following sections, we will introduce you to AWS CodeBuild and AWS CodePipeline along with setting up the different AWS resources we will be using to use our pipeline.
